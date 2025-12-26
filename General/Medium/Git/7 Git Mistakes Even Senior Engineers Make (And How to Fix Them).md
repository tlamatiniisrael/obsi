
## Learn the painful Git lessons I wish someone had warned me about

I was the Git expert on my team. The person everyone called when things broke.

Because I thought I had Git figured out, I started taking shortcuts. I skipped steps. I ran risky commands because they had never failed me before.

That overconfidence was the problem. The moment you think you know Git completely is the moment it bites back.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/1*IJ-sbPktMFY9FlZUd7FBtQ.png)

I learned that the hard way.

Here I am sharing seven Git mistakes I made, so that you can avoid them.

> Non-medium members [click here](https://medium.com/@vndpal/7-git-mistakes-even-senior-engineers-make-and-how-to-fix-them-32cbb1b2f90f?sk=16c1d6cc445d02af0a9b7250e7307744) to read for free.

## 1. Rebasing Shared Branches and Breaking Everyone

At a payments startup, three of us were working on the same feature branch. The history looked messy, so I ran a rebase to “clean it up.”

Everything looked beautiful on my machine.

But I had rewritten the commit history, which changed all commit IDs.

My teammates’ local copies were now incompatible with the rewritten version. When they pulled my updates, Git exploded with conflicts they couldn’t resolve.

We lost hours cleaning up a branch that had been perfectly fine before I touched it.

**Why does it happen:**

Rebase moves your commits onto a new starting point and gives them new IDs. Locally, everything looks fine because your history is rebuilt around those new commits.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/1*KzeWMLDwnnpWEYeNdPHTUA.png)

Source: [https://www.atlassian.com/git/tutorials/rewriting-history/git-rebase](https://www.atlassian.com/git/tutorials/rewriting-history/git-rebase)

The problem starts when others already have the original commits.

After the rebase, the shared branch points to a different history. Git sees the two versions as unrelated. Pulling then produces confusing conflicts that feel unnecessary but are technically correct.

**Lessons:**

- Rebase only on branches that exist in your local workspace.
- If a branch is already shared, use merge.
- If rewriting history is unavoidable, warn the team first so they can reset before pulling.

## 2. Treating Main as a Playground

In one system I worked on, seniors had direct push access to the main. It felt convenient. Small fix? Push. Quick hotpatch? Push.

Then one late night, a teammate pushed a fix that worked locally but failed a single integration test in CI. That halted deployments for two teams because everything flowed through that branch.

I still remember the annoyed Slack threads.

**Why does it happen:**  
Well, I would say, it’s sloppiness. We, senior engineers, underestimate risk. Most of us think that we don’t make rookie mistakes.

**Lessons:**

- Protect the main branch.
- Require pull requests, CI checks, and code review for everything.
- Small changes fail in big ways. Safety nets protect your future self.

## 3. Deleting Branches Without Confirming What’s Still Using Them

I once deleted a branch on the server, thinking it was merged. It wasn’t. It had a week of unmerged work that another teammate was still building on.

He pulled, saw the branch gone, and thought he’d lost everything. He hadn’t, but it took us some time to recover it from reflog and teach the team how Git stores objects.

**Why does it happen:**  
Most people don’t know that Git branches are just pointers. Delete the pointer too early, and other developers are stranded.

**Lessons:**

Before deleting a branch, check:

- Is it merged?
- Is anyone still using it?
- Did CI build it recently?

And teach your team how to recover deleted branches using `git reflog`. Most engineers don’t even know it exists.

## 4. Assuming Git History Is Forever Accessible

No, it is not. At scale, history disappears.

Repositories get archived. Shallow clones lose context. Squashed migrations erase trails. Force pushes remove forks. Old tags get deleted during cleanup.

This one was embarrassing.

During a third-party audit, we were asked to show a change. It had been made years earlier. This was before the company was acquired. It sounded simple. It was not.

No one could find the exact change. People started guessing.  
_“I think it was this commit.”  
“Maybe it was part of that refactor.”_

That is not good enough for an audit.

**Why does it happen:**

Many engineers assume Git is permanent. In reality, retention is a policy decision.

The people who worked on the system at the time had already left. They assumed Git would keep everything forever. So nothing was documented outside the repository.

By the time the audit occurred, the relevant history had been erased. What remained was incomplete. We could not reliably reconstruct what had actually changed.

**Lessons:**

- Treat critical repos as records, not just code.
- Lock down deletion of tags and release branches.
- Back up bare repositories separately.

If history matters, protect it like data.

## 5. Ignoring Tags and Making Releases Impossible to Trace

During one launch, dev, staging, and production were running different service versions. Some deployments were tagged, some weren’t. Some were manually tagged after deployment. Chaos.

When a bug hit staging, no one could answer the simplest question:

_“What exact commit is running right now on staging?”_

That’s a scary place to be.

**Why does it happen:**  
People assume tags are optional. They are not. Tags are the backbone of release traceability.

**Lessons:**

- Use automated tagging from CI.
- Every release should generate a tag that maps to a tested build.
- If you cannot find the code for a deployed version, production issues become hard to fix.

## 6. Writing Commit Messages That Explain What Happened but Not Why

I used to write messages like:

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/1*27qUtN_xsLdSm6_ZcxtBCg.png)

These messages tell nothing about intent. Six months later, I forgot why I made those changes. My teammates had no chance of knowing either.

During one outage, we found a commit called “quick fix.” It changed a critical validation rule. No explanation. We had to dig through Slack history to figure out why it was added.

**Why does it happen:**

When you are done with development and ready to commit the changes. It feels like our job is done here. Let’s quickly commit and get out of here. This leads to sloppy commit messages.

Commit messages are written fast. Reading them happens slowly. You borrow time from the future.

**Lessons:**

A good commit message answers two things:  
**1. What changed?**  
**2. Why did it need to change?**

The code already explains “how.” You need the message for “why.”

Instead of

quick fix validation error

Commit message like this

Fix validation for empty phone numbers.Checkout failed when phone was missing, added a safe default.

Now, you can easily see why this commit was made.

## 7. Letting Local Experiments Pollute the Repo

On a data pipeline project, we found dozens of branches with vague names. Some branches were “try-new-idea,” “temp,” and “test1.”

Some were months old. Some triggered CI. Some confused reviewers.

Git was drowning in noise.

![](https://miro.medium.com/v2/resize:fit:856/1*SX61M-aY9OOqH19hJbhRRg.png)

This is a nightmare for someone who is maintaining and managing the Git for your team.

**Why does it happen:**

Git feels like a safe backup space. What starts as temporary becomes permanent because no one cleans up.

**Lessons:**

- Keep experiments local or mark them clearly.
- Push them as backup, but auto-clean the stale branches regularly (e.g., monthly or quarterly).
- Use draft pull requests if you want to collaborate early without polluting history.

Clarity is a feature. Noise is a tax.

Every one of these mistakes began with good intentions.

- This will clean history.
- This will save time.
- This is just a quick fix.

Sometimes they even worked. Until they failed at the worst possible moment.

You do not become a Git pro by memorizing every command. You get there by building discipline around how you use it.

What Git lessons have you learned? Share your experience in the comments. It might help someone else.