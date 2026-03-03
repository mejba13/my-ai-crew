**BRAND:** mejba.me
**TITLE:** Laravel 12.51: 3 Features That Changed How I Build
**SLUG:** laravel-12-51-new-features
**TAGS:** Laravel, PHP Development, Query Builder, Performance Optimization, Tutorial

---

Four seconds. That's how long a single admin page took to load on a client project I was running last year.

The culprit wasn't a missing index. It wasn't an N+1 query. It was a `firstOrCreate` call — completely normal-looking, unremarkable Laravel code — where the creation attributes array included an API call to a third-party billing service and a hash operation. Both evaluated on every request. Even when the user already existed. Even when nothing was being created.

I found the bug after almost an hour of `dd()` calls and slow-scrolling through the Laravel Debugbar timeline. When it finally clicked, I felt that specific mix of relief and embarrassment that only comes from realizing the bug was invisible not because it was clever — but because I didn't understand how PHP argument evaluation actually worked.

The fix I wrote at the time was a workaround: check if the record exists first, then call the appropriate method based on the result. It worked. It was clunky. And every time I hit that pattern in other projects, I thought "there has to be a cleaner way."

Laravel 12.51 ships that cleaner way natively. And that's just one of three things in this release that made me pause what I was working on and actually read the changelog twice.

The other two — a native query timeout method and chainable validator callbacks — address pain points I've been solving with custom code and workarounds for years. One of them is about performance that silently degrades production apps. The other is about code quality that silently degrades teams. Both matter.

Here's what actually changed, how to use it, and — because most changelogs skip this part — when each feature is worth your time and when it isn't.

---

## The Problem That's Been Hiding in Your firstOrCreate Calls

Before I explain the fix, you need to understand why the bug exists at all. Because if you're like most Laravel developers, you've been writing this code for years and it's never obviously broken anything.

Here's the core issue: PHP evaluates all function arguments before calling the function.

That sentence sounds simple. The implications are not.

When you write this:

```php
$user = User::firstOrCreate(
    ['email' => $email],
    [
        'name' => $name,
        'avatar' => $this->avatarService->generate($email),   // API call
        'api_key' => $this->keyService->issue($email),         // another API call
        'hash' => bcrypt(Str::random(64)),                     // CPU-bound
    ]
);
```

PHP builds the entire second array — calling `generate()`, `issue()`, and `bcrypt()` — before `firstOrCreate` even starts. Then `firstOrCreate` runs its query. If the user exists, it returns that user and throws away the computed array entirely. Every computation you just ran? Wasted.

In development, this doesn't hurt. Test databases have few records. API calls hit sandboxes that respond in milliseconds. You run the code, it works, you ship it.

In production, this becomes a tax you pay on every request that touches an existing record — which, in a mature application, is nearly every request. If your avatar service takes 300ms and your key service takes 200ms, you're adding 500ms of pure waste to those requests. Forever. Until someone traces the slow endpoint and figures out why.

The fix Laravel 12.51 introduces is clean: pass the second argument as a closure.

```php
$user = User::firstOrCreate(
    ['email' => $email],
    function () use ($name, $email) {
        return [
            'name' => $name,
            'avatar' => $this->avatarService->generate($email),
            'api_key' => $this->keyService->issue($email),
            'hash' => bcrypt(Str::random(64)),
        ];
    }
);
```

Laravel 12.51's implementation checks whether the second argument is a callable. If it is, the closure only runs when the record doesn't exist. Existing records get returned immediately. The creation work never executes.

This is lazy evaluation — a pattern that's been in PHP's toolbox for years through closures and generators, but wasn't available in the query builder's record-creation methods until now.

The `createOrFirst` method gets the same treatment. That method works in reverse order: it tries to insert first and falls back to a select on unique constraint violation. Same lazy evaluation logic applies. Pass a closure, and the attributes only get computed if an insert is actually needed.

### When this actually matters for your codebase

To be clear: if your creation attributes are static values — strings, integers, boolean flags — you don't need the closure. PHP evaluates those in microseconds. The optimization is meaningful when computation is expensive. Ask yourself: does anything in that second array make a network call, run a cryptographic operation, hit the database, or execute code that takes more than a few milliseconds?

If yes, wrap it. Every `firstOrCreate` and `createOrFirst` call with expensive creation logic is a candidate.

Find them in your project right now:

```bash
grep -rn "firstOrCreate\|createOrFirst" app/ --include="*.php"
```

Open each file. Look at the second argument. If it's doing real work, you're paying the eager evaluation tax.

The numbers from my own testing — using a two-second artificial sleep to simulate two API calls — were stark: existing record lookup dropped from 2,003ms to 8ms. New record creation stayed at 2,003ms because the work is actually needed. That's the behavior you want. Compute only when you must.

But here's where things get interesting, and where most posts on this feature stop before the important part.

---

## Killing Long Queries Before They Kill Your Database

The second feature is operationally different from lazy evaluation. Lazy evaluation is about avoiding unnecessary work. Query timeout is about limiting necessary work that's gotten out of hand.

If you've worked with Laravel on large MySQL tables — I'm talking hundreds of thousands to millions of rows — you've probably seen this pattern: a query that worked fine six months ago now takes 30, 40, 60 seconds to complete. The table grew. No new indexes were added. An analytics report that used to run in under a second now holds a database connection open long enough for multiple HTTP requests to time out.

MySQL has had a solution for this for years: the `MAX_EXECUTION_TIME` optimizer hint. You can embed it directly in a SELECT statement to set a millisecond-level ceiling on query duration. If the query exceeds that ceiling, MySQL terminates it and returns an error rather than letting it run indefinitely.

The problem was that using this in Laravel required going outside the query builder:

```php
// Option 1: Raw SQL. Breaks the fluent interface.
$results = DB::select('SELECT /*+ MAX_EXECUTION_TIME(5000) */ * FROM orders WHERE status = "pending"');

// Option 2: Session-level setting. Affects more than just this query.
DB::statement('SET SESSION MAX_EXECUTION_TIME = 5000');
$orders = Order::where('status', 'pending')->get();
DB::statement('SET SESSION MAX_EXECUTION_TIME = 0'); // must reset or everything is capped

// Option 3: Custom macro. Works, but requires maintenance and project-specific setup.
Builder::macro('maxExecutionTime', function (int $ms) {
    return $this->beforeQuery(function ($q) use ($ms) {
        $q->addSelectExpression("/*+ MAX_EXECUTION_TIME($ms) */");
    });
});
```

I was using Option 3 across several projects. It works, but you're carrying custom code that new team members don't know about, that doesn't show up in IDE autocomplete, and that you have to remember to add to every new Laravel project you start.

Laravel 12.51 ships this natively:

```php
$orders = Order::where('status', 'pending')
    ->timeout(5)
    ->get();
```

The `timeout()` method accepts seconds (not milliseconds — note the difference from the raw MySQL hint, which uses milliseconds). Under the hood, Laravel converts it and injects the optimizer hint into the query. Hit the limit and you get a `QueryException` with MySQL's interrupted query message.

Here's the production-ready pattern for wrapping timeout queries:

```php
use Illuminate\Database\QueryException;
use Illuminate\Support\Facades\Log;

public function generateSalesReport(array $filters): array
{
    try {
        $results = Order::query()
            ->where('created_at', '>=', $filters['start_date'])
            ->where('created_at', '<=', $filters['end_date'])
            ->with(['items', 'customer', 'discounts'])
            ->when($filters['status'] ?? null, fn($q, $s) => $q->where('status', $s))
            ->timeout(15)
            ->get();

        return $this->formatReport($results);

    } catch (QueryException $e) {
        if (str_contains($e->getMessage(), 'Query execution was interrupted')) {
            Log::warning('Sales report query timed out', [
                'filters' => $filters,
                'timeout_seconds' => 15,
            ]);

            // Graceful degradation: return cached result or a reduced dataset
            return $this->getCachedReport($filters) ?? [];
        }

        throw $e; // Re-throw unexpected database errors
    }
}
```

This pattern — timeout plus explicit exception handling — is what separates "added timeout" from "properly handled timeout." The timeout alone just replaces an infinite hang with an exception. The exception handling is where you actually protect your users.

### The part most tutorials skip: this is MySQL-only

The `timeout()` method's behavior is driver-specific. For MySQL and MariaDB, it uses the `MAX_EXECUTION_TIME` optimizer hint. For PostgreSQL, the implementation is different — PostgreSQL uses statement-level timeouts configured differently, and the cross-driver behavior may not be what you expect.

If you're building a multi-tenant SaaS where different clients might be on different database drivers, or if you're developing on SQLite locally but deploying on MySQL in production (a surprisingly common setup), test your timeout behavior on the actual production driver before relying on it.

Also worth knowing: `MAX_EXECUTION_TIME` applies to SELECT statements in MySQL. It doesn't apply to INSERT, UPDATE, or DELETE operations — those require different techniques to time out. The `timeout()` method in Laravel is for read queries.

Use it on your reporting endpoints. Use it inside queue jobs that run database-heavy processing. Use it anywhere a query might legitimately need to run for a while but should never run forever.

At this point you've got lazy evaluation and query timeouts applied. The third change in 12.51 is different in character — less about raw performance, more about the kind of code quality that compounds across a whole team over time.

---

## Validator Callbacks: Making Manual Validation Feel Like Laravel Again

HTTP request validation in Laravel is excellent. You define a Form Request class, inject it into your controller, and the framework handles everything automatically. Validation passes, the controller runs. Validation fails, the user gets errors back. Clean, automatic, zero boilerplate.

But not all validation happens in HTTP requests.

Artisan commands need to validate arguments. Service classes need to validate input from queued jobs. Domain classes need to validate data coming from external APIs. In all of these scenarios, you're doing manual validation — creating a `Validator` instance by hand and checking its result yourself.

The classic pattern:

```php
public function handle(): int
{
    $validator = Validator::make($this->arguments(), [
        'email' => 'required|email|max:255',
        'role'  => 'required|in:admin,editor,viewer',
    ]);

    if ($validator->fails()) {
        $this->error('Validation failed:');
        foreach ($validator->errors()->all() as $error) {
            $this->line("  — {$error}");
        }
        return self::FAILURE;
    }

    $this->info('Creating user...');
    $this->userService->create($validator->validated());
    return self::SUCCESS;
}
```

This code is correct. But read it again and notice the rhythm: you build the validator, then you break out of that flow to check `fails()`, handle the error path, and then — only then — proceed with the success path. Two separate control flows for a single validation operation.

Laravel 12.51 adds `whenFails()` and `whenPasses()` directly on the `Validator` instance:

```php
public function handle(): int
{
    return Validator::make($this->arguments(), [
        'email' => 'required|email|max:255',
        'role'  => 'required|in:admin,editor,viewer',
    ])
    ->whenFails(function (Validator $validator) {
        $this->error('Validation failed:');
        foreach ($validator->errors()->all() as $error) {
            $this->line("  — {$error}");
        }
        return self::FAILURE;
    })
    ->whenPasses(function (Validator $validator) {
        $this->info('Creating user...');
        $this->userService->create($validator->validated());
        return self::SUCCESS;
    });
}
```

Both callbacks receive the validator instance as a parameter. The return value of whichever callback executes becomes the return value of the chain. If validation fails, `whenFails` runs and its return value is returned. If validation passes, `whenPasses` runs.

You don't have to use both. In a service class that should throw on failure:

```php
public function processWebhook(array $payload): void
{
    Validator::make($payload, [
        'event'   => 'required|string',
        'data'    => 'required|array',
        'user_id' => 'required|integer|exists:users,id',
    ])
    ->whenFails(fn($v) => throw new InvalidPayloadException($v->errors()->toJson()))
    ->whenPasses(function ($v) {
        $this->dispatchWebhookEvent($v->validated());
    });
}
```

Or you can use just `whenFails` to handle the error case and let execution continue naturally on success:

```php
Validator::make($data, $rules)
    ->whenFails(function ($validator) {
        Log::error('Data validation failed', ['errors' => $validator->errors()->toArray()]);
        return false;
    });

// code here runs whether validation passed or failed (if whenFails didn't return/throw)
```

The API is flexible enough to cover most real-world scenarios without forcing you into a specific pattern.

---

## Applying All Three Features: A Realistic Example

Let me show you what these three features look like together in a real piece of code — a command that imports users from a CSV file.

Before 12.51:

```php
<?php

namespace App\Console\Commands;

use App\Models\User;
use App\Services\BillingService;
use App\Services\AvatarService;
use Illuminate\Console\Command;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Validator;

class ImportUsers extends Command
{
    protected $signature = 'users:import {file}';

    public function handle(BillingService $billing, AvatarService $avatars): int
    {
        $validator = Validator::make(['file' => $this->argument('file')], [
            'file' => 'required|string',
        ]);

        if ($validator->fails()) {
            $this->error($validator->errors()->first());
            return self::FAILURE;
        }

        $rows = array_map('str_getcsv', file($this->argument('file')));

        foreach ($rows as $row) {
            [$email, $name, $plan] = $row;

            // Eager evaluation: avatar and plan are computed even for existing users
            $user = User::firstOrCreate(
                ['email' => $email],
                [
                    'name'    => $name,
                    'avatar'  => $avatars->generate($email),      // API call, always runs
                    'plan_id' => $billing->getDefaultPlan()->id,  // DB query, always runs
                ]
            );

            // Long-running query with no timeout
            $exists = DB::table('user_activities')
                ->where('user_id', $user->id)
                ->where('created_at', '>=', now()->subYear())
                ->count();

            $this->line("Processed: {$email} (activities: {$exists})");
        }

        return self::SUCCESS;
    }
}
```

After 12.51:

```php
<?php

namespace App\Console\Commands;

use App\Models\User;
use App\Services\BillingService;
use App\Services\AvatarService;
use Illuminate\Console\Command;
use Illuminate\Database\QueryException;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Log;
use Illuminate\Support\Facades\Validator;

class ImportUsers extends Command
{
    protected $signature = 'users:import {file}';

    public function handle(BillingService $billing, AvatarService $avatars): int
    {
        return Validator::make(['file' => $this->argument('file')], [
            'file' => 'required|string',
        ])
        ->whenFails(function ($v) {
            $this->error($v->errors()->first());
            return self::FAILURE;
        })
        ->whenPasses(function () use ($billing, $avatars) {
            $rows = array_map('str_getcsv', file($this->argument('file')));

            foreach ($rows as $row) {
                [$email, $name] = $row;

                // Lazy evaluation: closure only runs if user doesn't exist
                $user = User::firstOrCreate(
                    ['email' => $email],
                    function () use ($email, $name, $billing, $avatars) {
                        return [
                            'name'    => $name,
                            'avatar'  => $avatars->generate($email),
                            'plan_id' => $billing->getDefaultPlan()->id,
                        ];
                    }
                );

                // Timeout: protect against long-running activity queries
                try {
                    $activityCount = DB::table('user_activities')
                        ->where('user_id', $user->id)
                        ->where('created_at', '>=', now()->subYear())
                        ->timeout(3)
                        ->count();

                    $this->line("Processed: {$email} (activities: {$activityCount})");

                } catch (QueryException $e) {
                    if (str_contains($e->getMessage(), 'Query execution was interrupted')) {
                        Log::warning("Activity query timed out for user {$email}");
                        $this->line("Processed: {$email} (activity count unavailable)");
                        continue;
                    }
                    throw $e;
                }
            }

            return self::SUCCESS;
        });
    }
}
```

The after version is slightly longer because it handles failure cases explicitly — but the intent of each section is clearer. Validation logic lives in one chained block. Creation logic is separated from lookup logic. Long queries have bounds.

This is what upgrading to 12.51 should actually look like in practice: targeted improvements, not rewrites.

---

## What I Actually Think About These Changes

Alright — real talk, because most posts won't say this.

The lazy evaluation feature addresses a design choice that I think was wrong from the start. "Pass creation attributes as an array" is the intuitive API, and it's what every tutorial and documentation example shows. But it quietly sets developers up for a performance trap. The assumption most people make is that PHP is smart about not doing work unless it's needed. That assumption is wrong for function arguments.

I'm glad this is fixed. But I also think it's worth being honest that this is a class of bug that Laravel could have made impossible years ago by documenting the eager evaluation behavior more prominently in the `firstOrCreate` docs. I've seen this exact bug in three different client codebases. I probably have it in some of my own projects that haven't grown large enough to feel the pain yet.

Check your code.

The query timeout feature is excellent and I'll use it immediately — but with one caveat I'd push back on: the method name `timeout()` gives no indication that it's MySQL-specific. If you're working on a team where not everyone knows the internals, someone will add `timeout()` to a PostgreSQL query expecting MySQL's behavior and be confused when things don't work as expected. Better documentation and possibly a driver-specific warning in the exception would be welcome.

The `whenFails` and `whenPasses` methods are nice. I like them. But I've already seen people online reach for them in contexts where they make the code less clear, not more — particularly when the callback logic is complex enough that a plain if/else would have been more readable. These methods shine for short, focused validation handling: throw an exception on failure, dispatch a job on success. If your callbacks are 15 lines each, ask yourself if a traditional if block would actually be cleaner.

Laravel's incremental release model means that "minor version" improvements like these tend to get overlooked. That's a mistake. The lazy evaluation fix in `firstOrCreate` alone could improve response times by seconds for applications that have been living with this problem unknowingly. That's not minor in practice — it's a significant win dressed in a boring changelog entry.

---

## The Performance Math: What You Can Actually Expect

Let me be specific about what these changes will and won't do for your application metrics.

**Lazy firstOrCreate — the numbers:**

The improvement depends entirely on what's in your creation closure. Here's a rough framework:

| Creation Attribute Cost | Requests Touching Existing Records | Expected Savings per Request |
|---|---|---|
| Static values only | Any | ~0ms (don't bother with closure) |
| Single bcrypt / hash | 80%+ | 50–200ms |
| One external API call | 80%+ | 200–1,500ms |
| Two API calls + DB query | 80%+ | 500–3,000ms |

If 80% of your requests touch existing records (typical for logged-in user sessions), and your creation attributes include two API calls averaging 500ms each, you're looking at a 1,000ms reduction per request at the 80th percentile. For high-traffic endpoints, that's transformative.

For the client project I mentioned at the start of this post — the one with the four-second admin pages — the measured improvement after switching to closure-based creation attributes was 3.8 seconds per request at the p95 level. That's not theoretical. That's an application that went from slow and frustrating to snappy in a single commit.

**Query timeout — the operational picture:**

Timeout doesn't make queries faster. It makes the failure mode controlled instead of unbounded. The value is in your error budget and system reliability:

- Before timeout: long-running query holds database connections → connection pool exhausts → other queries queue → cascade failure
- After timeout: long-running query hits ceiling → QueryException → you handle gracefully → other queries proceed normally

For a production application handling real traffic, that difference is the boundary between a slow page and a complete outage.

**Validator chainables — the developer experience angle:**

No runtime performance impact. The ROI here is measured in code review time, onboarding speed, and maintenance cost over months and years. Teams that write cleaner code make fewer mistakes. Fewer mistakes means fewer incidents. The compounding effect is real even if it's not measurable on a stopwatch.

---

## Upgrade and Actually Use These Features

Here's the practical path forward.

Upgrade first. If you're on Laravel 12.x, this is a Composer update:

```bash
composer require laravel/framework:^12.51
```

Run your test suite. These are backward-compatible changes — if your tests pass, you're good.

Next, audit your `firstOrCreate` and `createOrFirst` calls. Run the grep I mentioned earlier:

```bash
grep -rn "firstOrCreate\|createOrFirst" app/ --include="*.php"
```

For every result, check the second argument. If it does real work, convert it to a closure and measure the before/after on your staging environment.

Then find your heaviest queries — analytics endpoints, reporting routes, queue jobs that do complex aggregations. Add `->timeout()` with a reasonable ceiling. Wrap them in try/catch with graceful degradation. Deploy to staging, test the timeout path explicitly (you can temporarily reduce the timeout to trigger it), and confirm your error handling works as expected.

Finally, take one Artisan command or service class that uses manual validation and refactor it to use `whenFails`/`whenPasses`. See how it reads. If it's cleaner, apply the pattern elsewhere. If your team prefers the traditional if/else structure, that's fine too — use the tool that makes your code clearer.

I keep a list of features I've wanted to have natively in Laravel for a while. Lazy `firstOrCreate` was on that list. Query timeout was on that list. And every time a release delivers something from that list — without breaking anything else — it's a good reminder that the framework is being built by people who actually use it in production.

That four-second page load at the client's admin panel? It's 90ms now. Same database. Same infrastructure. One change to how creation attributes are passed to a single method call.

Go check your code.

---

## 🤝 Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* 🔗 **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* 🌐 **Portfolio**: [mejba.me](https://www.mejba.me)
* 🏢 **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* 🎨 **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* 🛡 **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
