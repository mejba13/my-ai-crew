**BRAND:** mejba.me
**TITLE:** The Trolley Problem: Why It Haunts AI Builders
**META TITLE:** The Trolley Problem: Ethics Every AI Builder Faces
**SLUG:** trolley-problem-ai-ethics
**PRIMARY KEYWORD:** the trolley problem
**META DESCRIPTION:** The trolley problem isn't just a classroom riddle anymore. Here's how Bentham vs Kant shapes the AI agents I build and every choice we now hand to code.
**TAGS:** AI Ethics, Philosophy, Trolley Problem, Moral Reasoning, Explainer

---

# The Trolley Problem: Why It Haunts AI Builders

I was halfway through writing a retry policy for an agent — the boring kind of code, the kind nobody reviews — when I caught myself making a moral decision and pretending it was a technical one.

The agent could either roll back a half-finished database migration (safe, slow, annoying for the user who'd been waiting) or push forward and risk corrupting a few records to keep the other ninety-nine thousand intact. I typed the condition. I picked the ninety-nine thousand. And somewhere in the back of my head a voice I hadn't heard since I rewatched Michael Sandel's Harvard lecture said: *that's the trolley problem, and you just pulled the lever without noticing.*

That's the thing about **the trolley problem**. Most people file it under "fun philosophy riddle" — the runaway train, the five workers, the one guy on the side track. A party-trick dilemma. But if you build AI systems that make decisions on behalf of humans, you're not debating it in a seminar room. You're encoding your answer into an `if` statement, shipping it, and letting it run a million times a day while you sleep.

So I want to walk through the whole thing — the trolley, the fat man on the bridge, the doctor with five dying patients, and a genuinely disturbing 1884 cannibalism trial that ended up in English law textbooks. Not as a history lesson. As a map of the exact decisions we are now quietly handing to software.

By the end, you'll have two mental models that I promise will change how you read your own code. Stick with me to the doctor case — that's where most people's confidence falls apart, and it's the most useful part.

## Why a 50-year-old thought experiment suddenly matters

Sandel didn't invent the trolley problem. The philosopher Philippa Foot sketched the first version in 1967, and Judith Jarvis Thomson sharpened it in the seventies. What Sandel did was put it in front of nearly a thousand Harvard undergraduates in a packed lecture hall and film their reasoning falling apart in real time. That course — *Justice: What's the Right Thing to Do?* — became one of the most-watched university lectures ever recorded, and Lecture 1 is called "The Moral Side of Murder."

For most of those decades, the dilemma stayed theoretical. A clever way to make eighteen-year-olds argue. Nobody was actually building a machine that had to choose.

Now we are.

A self-driving car approaching an unavoidable collision is running a trolley problem in milliseconds. A triage algorithm in a hospital deciding which patient gets the last ICU bed is running one. The content-moderation model deciding whether to remove a post that might be a threat — or might be a joke — is running one. And the agents I build to manage infrastructure, money, and customer data are running smaller versions of it constantly, in code I wrote, reflecting an ethics I may not have examined.

That's why this stopped being abstract for me. I write the lever-pulling logic for a living now. If you ship anything that acts without a human in the loop, so do you. (I wrote a whole separate piece on [why AI-built systems still need human oversight](https://www.mejba.me/ai-web-design-human-oversight) — this is the philosophical floor underneath that argument.)

Here's the uncomfortable part, though. Before you can encode a good answer, you have to notice that your intuitions about these cases flatly contradict each other. Let me show you what I mean.

## The classic trolley: would you pull the lever?

Picture it. You're driving a trolley car barreling down the track at 60 miles an hour. Five workers are on the track ahead, tools in hand, backs turned. You hit the brakes. Nothing. The brake line is dead.

Off to the right, a side track. One worker on it. Your steering still works. You can turn.

Turn, and one person dies instead of five.

When Sandel asks a lecture hall full of people, the overwhelming majority say turn. Save the five. Most of us would. The arithmetic feels obvious — one death is a smaller tragedy than five, and if you have the wheel in your hands, you'd be a monster not to spin it. A small minority refuse, and some of them push hard: the moment you start justifying killing one innocent person for the "greater good," you've opened the door that totalitarian regimes have walked through to justify horrors. That's not a cheap rhetorical move. Hold onto it; it comes back.

But for now, note the headline: most people will sacrifice one to save five. Killing the one is the better choice. That feels like a principle.

Watch how fast the principle dies.

## The fat man on the bridge: same math, opposite gut

Same runaway trolley. Same five workers about to die. But now you're not the driver — you're a bystander standing on a footbridge over the track. Next to you, leaning over the railing, is a very large man.

You do the physics in your head. If you give him a shove, he falls onto the track, his body stops the trolley, and the five are saved. He dies. You don't have anything to throw — it has to be him, and it has to be your hands.

Would you push him?

Almost nobody says yes. The same people who confidently turned the wheel thirty seconds ago will not push the man. And here's what makes it philosophically savage: the math is *identical*. One dies, five live. If sacrificing one to save five was right on the track, why is it monstrous on the bridge?

This is the moment the riddle earns its reputation. Because now you have to explain the difference, and every explanation you reach for is slippery.

"On the bridge, I'd be *using* him — his body is the tool." Okay, but on the side track, isn't the lone worker also a tool of sorts? "Pushing is so direct, so physical." Is moral weight really a function of how close your hands are to the harm? "The fat man wasn't part of the situation — he was just standing there." But neither was the worker on the side track; he was just doing his job in a spot that happened to be safe until you redirected death toward him.

Every distinction you grab dissolves a little when you squeeze it. And yet the gut feeling is rock solid: turning the wheel is permissible, shoving the man is murder. Your intuition is certain about something your logic can't defend.

That gap — between what we feel and what we can justify — is the whole subject. It's also exactly the gap I fall into when I write decision logic and call a moral choice a "technical default."

## The doctor cases: where your confidence breaks for good

Sandel doesn't stop there, and neither will I, because the next pair is where the floor gives way.

Case one: you're an ER doctor. Six patients arrive from a crash. One is critically injured and will take all your time and resources to save. The other five have moderate injuries — but if you spend everything on the one, the five die. Spend your effort on the five, and the one dies. Most people say save the five. Same trolley logic, and it feels clean.

Case two: you're a transplant surgeon. You have five patients, each dying for want of a different organ — one needs a heart, one a liver, two kidneys, one lungs. In the next room, a perfectly healthy man is in for a routine checkup, napping. His organs are a match for all five.

Do you harvest him? Kill one healthy person, save five dying ones.

Nobody says yes. Not in any lecture hall, not in any room I've ever described it to. The revulsion is total.

But — and Sandel lands this like a hammer — *it's the same arithmetic again.* One life for five. If the numbers were the whole story, the surgeon should be sharpening the scalpel. The fact that every fiber of you screams *no* tells you the numbers were never the whole story. There is something about taking an innocent person and using them purely as a means to someone else's survival that we treat as categorically off-limits, no matter how good the math looks.

So which is it? Are we people who save five at the cost of one, or people who refuse to sacrifice the innocent? The honest answer is that we are both, depending on the case, and we mostly can't articulate the rule that switches between them.

Philosophers can. There are two great families of moral reasoning hiding underneath all four scenarios, and once you can name them, you'll see them everywhere — including, I'd bet, in the last feature you shipped.

## Consequentialism vs. categorical: the two operating systems of morality

Here are the two mental models. I think of them as two different operating systems for making a decision.

**Consequentialist reasoning** says the morality of an act lives entirely in its consequences. You judge the outcome. Best result wins. Better that five live and one dies than the reverse — count the bodies, do the subtraction, choose the smaller number. The lever, the side track, the five-patient ER: all consequentialist verdicts. The headline form of this is **utilitarianism**, and its founding father is Jeremy Bentham, an English philosopher writing in the late 1700s.

Bentham's claim is almost aggressively simple. "Nature has placed mankind under the governance of two sovereign masters, pain and pleasure." The right thing to do, always, is whatever maximizes the balance of pleasure over pain across everyone affected. Add up the happiness, subtract the suffering, pick the highest score. The slogan you've heard — *the greatest good for the greatest number* — is the bumper-sticker version. John Stuart Mill, the next great utilitarian, would later try to make it more humane and less of a pleasure-calculator, but the engine is the same: outcomes are what count.

**Categorical reasoning** says no. Certain acts are right or wrong *in themselves*, regardless of consequences. Some duties and rights are absolute. You don't get to murder an innocent person even if the spreadsheet says it nets positive happiness, because the act of murdering an innocent is categorically wrong, full stop. The bystander who won't push the fat man, the surgeon who won't harvest the patient — that's categorical reasoning overriding the math. Its towering figure is Immanuel Kant, the German philosopher, whose core rule is that you must never treat a person *merely as a means* to an end. People are not raw material for someone else's outcome.

Now reread the four scenarios with those two labels in hand. The trolley driver and the triage doctor are letting consequentialism drive. The bridge bystander and the transplant surgeon are letting the categorical rule slam the brakes. Same person, same day, two different operating systems — and the switch between them is the thing we can't explain but feel absolutely.

This isn't trivia. It's the fork in the road for anyone designing automated decisions. When I write an agent's policy, I am — whether I admit it or not — choosing which operating system runs. And those two systems give *opposite answers* in the cases that matter most.

If you want to see how badly the two collide when real lives and real consequences are stacked against each other, there's no cleaner example than a real trial. Which brings me to the part of this I can't shake.

## Queen v. Dudley and Stephens: when the trolley problem went to court

In 1884, the yacht *Mignonette* sank in a storm roughly 1,600 miles off the Cape of Good Hope. Four men made it into a tiny lifeboat: the captain, Tom Dudley; the mate, Edwin Stephens; a sailor, Edmund Brooks; and the cabin boy, a seventeen-year-old named Richard Parker.

They had two tins of turnips and no fresh water. For about nineteen days they drifted — eating a turtle they caught, drinking rainwater when it came, and eventually drinking their own urine when it didn't. Parker, the youngest, ignored the others' warnings and drank seawater. He fell sick, slipped toward a coma, and lay dying in the bottom of the boat.

On roughly the twentieth day, Dudley proposed it out loud: they should draw lots. One of them would die so the others could eat and live. Brooks refused to take part. No lottery was ever held. Instead, Dudley — with Stephens agreeing — decided. He said a prayer, told the boy his time had come, and killed Richard Parker with a penknife. The three surviving men fed on his body for four days until a passing German ship, the *Moctezuma*, picked them up and brought them home to Falmouth, England, on September 6, 1884.

And here's the part that stops me cold every time: Dudley didn't hide it. He gave a full, honest account to the authorities, apparently assuming any reasonable person would understand. He genuinely believed necessity made it forgivable. Instead, he and Stephens were tried for murder.

(One eerie footnote, because it's true and too strange to skip: forty-six years *earlier*, Edgar Allan Poe published a novel in which shipwrecked sailors draw lots and cannibalize a cabin boy named — Richard Parker. Life imitated fiction down to the name. Make of that what you will.)

## The defense and the prosecution: both arguments are the trolley problem

Listen to how the two sides argued, because you've already met both of them.

**The defense was pure consequentialism.** Necessity. These were desperate men facing certain death. Parker was dying anyway and had no dependents, while the others had families. By acting, three lives were saved at the cost of one that was nearly spent. Better that three live than four die. If you turned the trolley wheel, you already accept the shape of this argument. The defense was, in essence, asking the court to count the bodies.

**The prosecution was pure Kant.** Murder is murder. You do not get to appoint yourself the arbiter of who lives and who dies, no matter how dire the circumstances. The boy was an innocent human being, not a resource to be consumed when the math turned grim. To kill him was to use him *merely as a means* — the exact line Kant said you may never cross. Desperation explains the act; it does not justify it.

The court sided with the prosecution. Dudley and Stephens were convicted of murder. The judges famously refused to let necessity become "a legal cloak for unbridled passion and atrocious crime" — because once you allow killing the innocent when the numbers favor it, where exactly does the line go? They were sentenced to death, though the sentence was almost immediately commuted to six months in prison. The law had drawn a categorical boundary: there are things you may not do to an innocent person, even to save more people, even to save yourself.

That ruling is still cited in common-law courts nearly a century and a half later. A handful of starving men in a lifeboat accidentally wrote one of the firmest lines in Western law about what consequences can and cannot buy.

This is also where I want to slow down, because there's a wrinkle that I think matters more than the verdict — and it's the one that's most relevant to AI.

## Does consent change everything?

Sandel pushes his students on a question that reorganized how I think about every automated decision: *what if Parker had agreed?*

Suppose the four men had drawn lots, all consenting to the procedure in advance, and Parker had drawn the short straw. Or suppose, before he fell ill, Parker had said: if it comes to it, take me. Would the killing then be permissible?

A lot of people's intuitions flip here. With a fair lottery that everyone agreed to, the act stops feeling like murder and starts feeling like a tragic contract. Consent seems to launder the wrongness. The man on the bridge becomes monstrous because he never agreed to be a brake; a soldier who volunteers for a suicide mission is honored, not avenged. So maybe the magic ingredient isn't the body count at all — it's *agreement*.

But squeeze that too, and it leaks. Was Brooks's silence consent, or coercion by circumstance? Can consent given under the threat of starvation ever be free, or is it just despair wearing the costume of choice? And there's a darker version: if a lottery is legitimate, what stops the strong from "lotteries" that always seem to land on the weak? Procedural fairness can be a genuine moral upgrade — or a thin coat of paint over the same old domination.

I sat with that for a long time, because consent is *exactly* the lever we pull in software to make hard things feel acceptable. The terms of service you didn't read. The "I agree" checkbox before the algorithm decides your loan, your feed, your insurance rate. We lean on consent to transform an act we'd otherwise find unacceptable into one we wave through. Sandel's lifeboat is asking the question every product team should ask: *is this real autonomy, or is it consent extracted under conditions where no one could meaningfully refuse?*

I don't have a closed-form answer. Neither did the lecture hall. But noticing the question is the entire point.

## What this actually means for the code we ship

Here's where I land after letting all of this rattle around for weeks, and where I think it earns a place on a blog mostly about building with AI.

Every autonomous system encodes a moral operating system, whether its builders chose one on purpose or not. When you write "minimize total errors" as your objective function, that's Bentham in a lab coat — consequentialism, optimizing the aggregate, willing to accept that a few individuals get crushed so the average improves. When you hard-code "never expose a user's private data, even if it would improve the model for everyone," that's Kant — a categorical rule that refuses to treat the individual as raw material for the collective good.

Most ML systems default to consequentialism, because optimization *is* consequentialism. You pick a metric and maximize it. The whole field is a body-count calculus dressed in linear algebra. And that's often fine — for ad ranking, for routing, for compression. Nobody needs Kant to sort a queue.

But the trolley problem is the warning label. The moment your system touches lives, liberty, money, or dignity, pure optimization starts quietly shoving fat men off bridges, and it'll do it with a clean conscience because the loss function went down. The reason we build guardrails, refusal behaviors, and hard constraints into AI agents is that we've decided — correctly — that some acts should be categorically off the table no matter how good they'd score. That's not a technical decision. It's Kant overruling Bentham, implemented in a config file.

This connects to a pattern I keep running into across very different problems. When I wrote about [the AI layoff trap and its prisoner's-dilemma logic](https://www.mejba.me/ai-layoff-trap-pigovian-tax-prisoners-dilemma), the failure was a pure consequentialist calculation — each firm optimizing its own numbers — producing a collectively catastrophic outcome. When I covered [the debate over AI-discovered zero-days](https://www.mejba.me/ai-zero-day-discovery-debate), the entire fight was consequentialist defenders ("more security overall") versus a categorical objection ("don't hand the world a weapon"). I didn't have the vocabulary then to see they were the same argument in different clothes. I do now. That's the transformation I'm hoping to hand you.

If you take one practical thing from all of this, let it be this habit: **when you write a rule that decides something for a human, stop and ask which operating system you just installed.** Are you counting bodies, or honoring a boundary? There's no universally right answer. But there's a world of difference between choosing on purpose and sleepwalking into a moral stance because the optimizer was easier to write.

## The one move that's never allowed: pretending the question isn't there

There's a tempting exit from all of this. The skeptic's exit. *These problems can't really be solved. Morality is just personal preference. Who's to say?* I've heard engineers reach for it the second a discussion gets uncomfortable, and I've reached for it myself.

Sandel borrows Kant's response, and it's the sharpest thing in the lecture. Skepticism, Kant wrote, is "a resting-place for human reason" — somewhere to pause and survey the difficulty. But it is "no dwelling-place for permanent settlement." You're allowed to rest there. You are not allowed to *live* there. Throwing up your hands and declaring it all unanswerable isn't humility. It's an evasion — a way to avoid the work of actually reasoning through what you believe and why.

For people building systems that decide things for other people, the skeptic's exit isn't just lazy. It's negligent. The code ships regardless. The agent acts regardless. "Who's to say?" doesn't stop the trolley; it just means you turned the wheel with your eyes closed.

## Frequently Asked Questions

### What is the trolley problem in simple terms?
The trolley problem is a thought experiment where a runaway trolley will kill five people unless you divert it to a track where it kills one. It forces a choice between actively causing one death to prevent five, exposing the conflict between judging actions by their outcomes and judging them by absolute moral rules. For the full breakdown and its variations, see the scenarios above.

### What's the difference between consequentialism and categorical reasoning?
Consequentialism judges an act entirely by its consequences — the best outcome is the moral one, as in utilitarianism's "greatest good for the greatest number." Categorical reasoning holds that some acts are right or wrong in themselves regardless of outcome, such as Kant's rule that you must never use a person merely as a means.

### Why won't people push the fat man if they'd pull the lever?
Both choices sacrifice one life to save five, but pulling the lever feels like redirecting an existing threat while pushing the man feels like using him directly as a tool to stop it. This gap between identical math and opposite intuitions is exactly what the trolley problem is designed to expose — see the doctor cases above for the sharpest version.

### How does the trolley problem apply to AI?
Any AI system that acts without a human in the loop — self-driving cars, triage algorithms, autonomous agents — must resolve trolley-style trade-offs in code. Optimizing a metric is consequentialist by default, while guardrails and hard refusals are categorical rules, so builders are encoding a moral stance whether they realize it or not.

### What happened in Queen v. Dudley and Stephens?
In 1884, shipwrecked sailors Tom Dudley and Edwin Stephens killed and ate dying cabin boy Richard Parker to survive, then were tried for murder. The English court rejected their "necessity" defense, establishing that necessity is not a defense to murder and that killing an innocent person is not justified by saving more lives.

## You're already pulling levers

Go back to my retry policy — the boring code I opened with. I chose the ninety-nine thousand records over the one user. Bentham would have nodded. But I made that call without ever asking whether there was a categorical line I shouldn't cross, whether the one user had a right that the aggregate couldn't buy, whether "consent" buried in a terms-of-service nobody reads was doing real moral work or just covering me.

That's the shift I want to leave you with. The trolley problem was never really about trolleys. It's about the fact that you cannot act in the world — or write software that acts in the world — without taking a moral position, and the only real choice is whether you take it consciously or by accident.

So here's your challenge for the next twenty-four hours. Open the last thing you built that makes a decision about a person — a ranking, a threshold, a filter, an automated action. Find the line where it chooses. And ask yourself one question: *did I count bodies, or did I honor a boundary — and would I be able to defend it to the person on the other end of that decision?*

If you can answer that, you're already doing the work Sandel was trying to provoke. If you can't, you've just found the most important code review you'll do all year.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
