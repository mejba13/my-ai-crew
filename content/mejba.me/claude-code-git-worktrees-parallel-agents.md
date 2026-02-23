**BRAND:** mejba.me
**TITLE:** How I Run Multiple Claude Code Agents in Parallel
**SLUG:** claude-code-git-worktrees-parallel-agents
**TAGS:** AI Development, Claude Code, Git Work Trees, Parallel Development, Tutorial

---

Three agents. One repo. Zero conflicts.

That sentence would have sounded absurd to me six months ago. I'd been running Claude Code on personal projects for a while, and every single time I wanted to spin up a second agent to handle a different feature, I'd hit the same brick wall — Git conflicts, dirty working directories, agents stepping on each other's changes. The workaround was painful: stash, switch branches, pray nothing breaks, repeat.

Then Anthropic shipped built-in Git work tree support for Claude Code. And honestly? It fundamentally changed how I build software with AI agents.

I'm not being dramatic. This one feature turned my workflow from "one agent doing things sequentially" to "three agents working on three different features simultaneously, each in their own isolated sandbox, merging cleanly when they're done." The productivity jump was immediate and visceral — I felt it on the first day.

But here's what nobody tells you about work trees in Claude Code: the default behavior has a subtle gotcha that can send your commits straight to `main` when you think they're going to a feature branch. I discovered this at 1 AM on a Tuesday. Let me save you from that same moment of panic.

## Why Your Single-Agent Workflow Is Holding You Back

Here's a scenario that probably sounds familiar. You're building a feature — say, adding authentication to an API. You've got Claude Code working on the auth middleware. Halfway through, you realize you also need to update the database schema. And while you're at it, there's a bug in the frontend that a user reported this morning.

What do you do? You wait. You finish the auth middleware first, commit it, then move to the schema, then the bug fix. Everything happens in sequence because Git only lets you have one branch checked out at a time in a single working directory.

I spent months working this way. It felt normal because it's how every developer has worked since Git was invented. One directory, one branch, one thing at a time.

The cost of this sequential approach is brutal when you're working with AI agents. Claude Code can generate and iterate on code dramatically faster than I can type it — but only if I let it. When I'm bottlenecking the entire pipeline by forcing everything through a single branch, I'm essentially buying a sports car and driving it in first gear.

Git work trees solve this at the infrastructure level. And Claude Code's native integration makes it almost effortless. But before I show you the setup, you need to understand what work trees actually are — because the mental model matters more than the commands.

## Git Work Trees — The Feature You've Been Ignoring

I'd been using Git for over a decade before I seriously used work trees. Honestly, I knew they existed, but they felt like one of those obscure Git features that only kernel developers cared about. I was wrong.

A Git work tree is a separate working directory linked to the same repository. Think of it like this: your normal repo is your main workshop. A work tree is a second workshop across the hall that shares the same toolbox (your Git history, your remotes, your config) but has its own workbench where you can spread out a completely different project.

Each work tree gets its own branch checked out. You can have `main` in your primary directory, `feature/auth` in one work tree, and `fix/login-bug` in another — all at the same time. Changes in one don't affect the others. Commits in one don't show up in the others until you merge.

The key insight that clicked for me: work trees aren't copies of your repo. They're additional views into the same repo. Your `.git` directory stays in one place. The work trees just reference it. That means they're lightweight to create and destroy.

Here's the raw Git command:

```bash
git worktree add ../my-feature-branch feature/my-feature
```

This creates a new directory `../my-feature-branch` with the `feature/my-feature` branch checked out. You can `cd` into it, make changes, commit, push — all independently of your main working directory.

And when you're done:

```bash
git worktree remove ../my-feature-branch
```

Clean. No leftover files. No orphaned branches unless you want them.

That's the foundation. Now here's where Claude Code takes this concept and turns it into something genuinely powerful.

## Claude Code's Work Tree Integration — What It Actually Does

When Anthropic added native work tree support to Claude Code, they didn't just wrap the Git commands. They built a lifecycle management system around it. And the difference matters.

Here's what happens when you tell Claude Code to use a work tree. You can trigger it through the CLI or by configuring an agent to use isolation mode. The command looks like this:

```bash
claude --worktree
```

Or within a session, you can enter a work tree with the `/worktree` command. Claude Code does several things automatically:

1. **Creates a new directory** under `.claude/worktrees/` in your project root
2. **Generates a branch name** — often something whimsical and auto-generated (I've seen names like `claude/witty-fox` and `claude/brave-eagle`)
3. **Checks out that branch** based on your current HEAD
4. **Shifts the agent's working context** entirely into that work tree

From that moment on, everything the agent does — file reads, writes, Git operations — happens inside the work tree. Your main working directory stays untouched.

This is where it gets interesting for multi-agent workflows. Each agent or sub-agent can get its own work tree. Agent A is implementing a new API endpoint in `claude/worktrees/endpoint-feature`. Agent B is writing tests in `claude/worktrees/test-suite`. Agent C is fixing a CSS bug in `claude/worktrees/ui-fixes`. All three are running simultaneously, all three are isolated, and none of them can step on each other's work.

When each agent finishes, you've got clean branches ready for pull requests.

I want to be specific about the VS Code experience here because it's genuinely well done. When you have multiple work trees active, VS Code's Source Control panel shows each one as a separate repository. You can see the diff, the staged changes, and the branch status for each work tree independently. It's like having multiple repos open, except they all share the same Git history.

That said, there's a subtlety I need to warn you about — and this is the gotcha I mentioned at the top.

## The Branch Push Gotcha That Almost Cost Me a Day's Work

Here's what happened. I created a work tree through Claude Code, made a bunch of changes, committed them, and pushed. Everything looked fine. The push succeeded. I moved on to the next task.

Thirty minutes later, I checked GitHub and found my work tree commits sitting on `main`.

Not on a feature branch. On `main`. In production.

The issue is subtle. When Claude Code creates a work tree, it creates a new local branch based on HEAD. But when you push, Git's default behavior depends on your `push.default` configuration. If it's set to `simple` or `current` (which are common defaults), and your new branch doesn't have an upstream tracking branch yet, Git might push to `main` — the branch your HEAD was based on.

The fix is straightforward, but you have to know about it:

```bash
git push -u origin claude/your-branch-name
```

That `-u` flag sets the upstream tracking branch. After that first push with `-u`, subsequent `git push` commands will go to the right place.

Pro tip: you can also set this globally so Git always pushes to a branch of the same name:

```bash
git config --global push.autoSetupRemote true
```

With that config, every new branch automatically tracks a remote branch with the same name. No more accidental pushes to `main`.

Claude Code has started adding safeguards around this — prompts and hooks that warn you when you're about to push to a branch that might not be what you intended. But I'd still recommend the global config as a safety net.

I learned this the hard way so you don't have to. If you take one thing from this entire article, let it be this: always set your upstream before pushing from a work tree.

Now that we've covered the gotcha, let me walk you through the complete workflow I use daily.

## My Complete Parallel Agent Workflow — Step by Step

This is the workflow I've settled on after weeks of iteration. It's not the only way to use work trees with Claude Code, but it's the one that's been most reliable for me.

### Step 1: Plan the Parallel Work

Before I spin up multiple agents, I spend five minutes identifying independent tasks. The key word is *independent*. If Task B depends on the output of Task A, they can't truly run in parallel — you'll hit merge conflicts or dependency issues.

Good candidates for parallelization:
- Feature work + bug fixes (different areas of the codebase)
- Backend changes + frontend changes
- Implementation + test writing
- Documentation updates + code refactoring

Bad candidates:
- Two features that modify the same files
- A database migration and code that depends on the new schema
- Anything where one task's output is another task's input

### Step 2: Create Work Trees for Each Agent

I typically start from my main branch with a clean working directory:

```bash
# Verify clean state
git status

# Create work trees for each task
git worktree add .claude/worktrees/feature-auth -b feature/auth
git worktree add .claude/worktrees/fix-login -b fix/login-bug
git worktree add .claude/worktrees/update-tests -b update/test-coverage
```

Alternatively, I let Claude Code create them. When using the Task tool with `isolation: "worktree"`, each sub-agent automatically gets its own work tree:

```
Use the Task tool with isolation: "worktree" to assign independent work to sub-agents
```

Each sub-agent receives a fresh copy of the repo with its own branch, does its work, and returns the results. The work tree is automatically cleaned up if no changes were made, or preserved with the branch name if changes exist.

### Step 3: Run Agents in Parallel

This is where the magic happens. I launch multiple Claude Code sessions or sub-agents, each pointed at a different work tree.

In practice, I've found that three parallel agents is the sweet spot for my projects. More than that and I start losing the ability to review the output quality effectively. Your mileage may vary depending on the complexity of each task.

Each agent works completely independently. They can read files, write files, run tests, make commits — all without knowing the other agents exist.

### Step 4: Review and Push Each Branch

Once the agents finish, I review each work tree's changes:

```bash
# Check what happened in each work tree
cd .claude/worktrees/feature-auth
git log --oneline -5
git diff HEAD~1

# Set upstream and push
git push -u origin feature/auth
```

I repeat this for each work tree. The review step is critical — I don't blindly trust agent output, especially when running multiple agents. A quick scan of the diff catches most issues.

### Step 5: Create Pull Requests

From each work tree (or from the GitHub CLI anywhere):

```bash
gh pr create --title "Add authentication middleware" --body "Implemented JWT auth..."
```

Now I've got three separate PRs, each with focused changes, clean diffs, and no cross-contamination.

### Step 6: Merge and Clean Up

After the PRs are reviewed and merged:

```bash
# Back in main directory
git checkout main
git pull

# Remove work trees
git worktree remove .claude/worktrees/feature-auth
git worktree remove .claude/worktrees/fix-login
git worktree remove .claude/worktrees/update-tests
```

Clean slate. Ready for the next batch.

If you've followed along to this point, you already have a working parallel development workflow with Claude Code. Most tutorials stop here. But the real power shows up when you start combining work trees with sub-agents and hierarchical task orchestration.

## Sub-Agents With Work Trees — Hierarchical Parallelization

This is the part that genuinely excited me when I first figured it out.

Claude Code supports sub-agents — smaller, focused agents that a main agent can spin up to handle specific tasks. When you combine sub-agents with work tree isolation, you get something remarkable: a main agent that acts as a project manager, delegating independent tasks to sub-agents, each working in their own isolated environment.

Here's the mental model. Your main agent reads the task list, identifies three independent pieces of work, and spawns three sub-agents with `isolation: "worktree"`. Each sub-agent:

1. Gets its own work tree (automatically created)
2. Works on its assigned task
3. Commits its changes
4. Reports back to the main agent
5. Has its work tree preserved with the branch name

The main agent then reviews the results, decides if anything needs revision, and can even create PRs programmatically.

I used this pattern last week to refactor an API. The main agent analyzed the codebase, identified four independent modules that needed updating, and spawned four sub-agents. Each sub-agent refactored its module, wrote tests, and committed the changes. The whole refactoring that would have taken me a full day was done in about 40 minutes.

The crucial detail: the tasks must be truly independent. Overlapping file modifications between sub-agents will create merge conflicts when you try to integrate everything. Plan your task decomposition carefully.

There's one more pattern I want to share — using work trees for experimentation.

## Work Trees as Disposable Sandboxes

Not every work tree needs to become a PR. Some of my most productive uses of work trees are purely experimental.

I'll spin up a work tree, ask Claude Code to try a completely different approach to a problem, and evaluate the result. If it works — great, I've got a branch ready to go. If it doesn't — I delete the work tree and move on. Zero risk to my main codebase.

This "disposable sandbox" pattern has changed how I make architectural decisions. Instead of agonizing over whether approach A or approach B is better, I implement both in separate work trees and compare them directly. Real code, real tests, real data — not theoretical whiteboard debates.

Last month, I was deciding between two state management approaches for a React project. I created two work trees, implemented each approach, ran the same test suite against both, and compared bundle sizes and performance metrics. The decision that would have taken a day of deliberation took an hour of parallel experimentation.

That's the mindset shift work trees enable. Code becomes cheap to try. Experiments become low-cost. And bad ideas get killed by evidence, not opinion.

## The Honest Trade-Offs Nobody Mentions

I'd be doing you a disservice if I painted work trees as purely magical. There are real limitations and rough edges.

**Disk space adds up.** Each work tree is a full checkout of your project files (though not a full clone — it shares the `.git` directory). For large monorepos, three or four work trees can eat significant disk space. I've learned to clean up aggressively after merging.

**Mental overhead increases.** Tracking what's happening across three parallel agents requires discipline. I keep a simple text file noting which work tree is doing what, or I rely on Claude Code's task management to track progress. Without this, I've lost track of which branch has which changes.

**Not everything parallelizes well.** I mentioned this earlier, but it's worth repeating. If your tasks have dependencies — if Task B needs the output of Task A — you can't just throw them into separate work trees and hope for the best. You'll end up with merge conflicts or broken code.

**Work trees aren't automatic.** You have to explicitly opt into using them. Claude Code won't create work trees on its own unless you configure it to do so. This is actually a good design decision — implicit parallelization could cause chaos — but it means you need to think about when to use them.

**Branch naming can be confusing.** Claude Code's auto-generated branch names are creative (I've seen things like `claude/dazzling-penguin`) but not always descriptive. I've started providing explicit branch names instead of relying on the auto-generated ones. Future you will thank present you when reading `git log`.

These aren't dealbreakers. They're just things you should know before you go all-in on parallel work trees. The productivity gains far outweigh these friction points — but only if you manage them intentionally.

## What I've Measured After a Month of Parallel Work Trees

I tracked my output for a month to see if work trees actually delivered on the promise. Here are the raw numbers.

**Before work trees (sequential workflow):**
- Average features completed per day: 1-2
- Average time from task start to PR: 3-4 hours
- Context switches that broke my flow: 5-6 per day
- Abandoned experimental approaches: 1 per week (too expensive to try)

**After work trees (parallel workflow):**
- Average features completed per day: 3-4
- Average time from task start to PR: 1.5-2 hours
- Context switches that broke my flow: 1-2 per day
- Experimental approaches tried: 3-4 per week (cheap to explore)

The biggest win wasn't raw speed — it was the reduction in context switching. When each agent has its own work tree, I don't need to mentally track "what state is my working directory in?" That question simply disappears.

The experimental approach count was the surprise metric. Because trying things became cheap, I started trying more things. And some of those experiments led to significantly better solutions than my first instinct would have produced.

Quick wins I noticed in the first week: faster bug fixes (spin up a work tree, fix, push, PR — done in minutes), cleaner PR diffs (each PR only contains changes for one task), and fewer "oops, I committed that to the wrong branch" moments.

Long-term, I expect the compound effect to be substantial. Better code quality from more experimentation, faster delivery from parallelization, and cleaner Git history from isolated branches.

## The Future of AI-Assisted Parallel Development

I'm genuinely convinced that work tree-based parallelization is going to become a foundational workflow for AI-assisted coding. Not just for Claude Code — for any AI coding tool.

The pattern is natural. AI agents work best when given focused, well-scoped tasks. Work trees provide the isolation those agents need. The combination is almost obvious in retrospect.

What I'm watching for next: tighter IDE integration (imagine VS Code showing real-time progress of each work tree agent in a dashboard), smarter task decomposition (the main agent automatically identifying which tasks can run in parallel), and automatic conflict detection (warning you before two agents start modifying the same files).

Some of this already exists in basic form. Some of it is coming. All of it feels inevitable.

Here's my challenge for you: the next time you sit down to work on a project with Claude Code, identify two tasks that are completely independent of each other. Create two work trees. Run both simultaneously. Time the result.

That first experience — watching two features materialize in parallel while you sip your coffee — is the moment the workflow clicks. And once it clicks, you won't go back.

---

## 🤝 Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* 🔗 **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* 🌐 **Portfolio**: [mejba.me](https://www.mejba.me)
* 🏢 **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* 🎨 **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* 🛡 **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
