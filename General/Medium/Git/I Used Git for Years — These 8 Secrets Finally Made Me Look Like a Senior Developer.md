
![](https://miro.medium.com/v2/resize:fit:1400/1*uMCYtvPWRoVHAnuVRnrWyg.png)

## 1. `git pull` Is Not a Single Command (And That Matters)

Most developers think:

```
git pull
```

means “get latest code”.

That’s only half the story.

What `git pull` _actually_ does is:

1. `git fetch`
2. `git merge` (or rebase, depending on config)

This matters because **merge conflicts don’t come from fetch — they come from merge**.

If you want control, stop using `git pull` blindly.

Instead:

```
git fetch  
git status  
git diff
```

Now _you_ decide what to merge, when to rebase, and how to resolve conflicts.

This single habit makes you look like a senior dev overnight.

## 2. Your Commit History Is a Story — Not a Trash Bin

I used to commit like this:

- fix bug
- fix bug again
- final fix
- really final fix
- pls work

Then I wondered why reviewers hated my PR.

Here’s the secret:  
**Your commit history is documentation.**

Before pushing, you can clean it up:

```
git rebase -i HEAD~5
```

You can:

- squash commits
- rename messages
- remove noise

A clean history says:

> _“I know what I’m doing.”_

A messy history says:

> _“I panic and push.”_

Your teammates won’t say it out loud — but they notice.

## 3. `git status` Is the Most Underrated Command

When things go wrong, most people panic and Google random commands.

Experienced devs do one thing first:

```
git status
```

It tells you:

- what branch you’re on
- what files are changed
- what’s staged
- what’s not

Before **any** Git command:

1. Run `git status`
2. Read it slowly
3. Then decide

This habit alone prevents 80% of Git disasters.

## 4. Branch Names Are Signals, Not Just Labels

Some teams use branch names like:

```
test  
new  
fix  
temp
```

That’s chaos.

Good branch names tell a story:

```
feature/user-login  
bugfix/payment-timeout  
hotfix/prod-crash
```

Why this matters:

- Code reviewers instantly understand context
- CI tools behave better
- Rollbacks become easier

Bad branch names don’t break Git —  
they break **team communication**.

## 5. `git stash` Is a Safety Net — Use It Properly

Many devs either:

- never use `git stash`
- or use it and forget what they stashed

The secret is **naming your stash**:

```
git stash push -m "WIP: refactoring order service
```

Later:

```
git stash list  
git stash apply stash@{0}
```

Stash is not a dumping ground.  
It’s a _temporary parking space_.

If you stash without meaning, you’ll lose work mentally — even if Git still has it.

## 6. Force Push Is Not Evil — But It Is Dangerous

You’ll hear this rule everywhere:

> _“Never use force push.”_

That’s not true.

The real rule is:

> **_Never force push to a shared branch._**

Force push is perfectly fine on:

- your personal feature branch
- before opening a PR
- when cleaning history

Command:

```
git push --force-with-lease
```

Not `--force`.

`--force-with-lease` protects you from overwriting someone else’s work.

Your teammates won’t explain this…  
They’ll just yell if you get it wrong.

## 7. Merge Conflicts Are Not Random — They Are Predictable

Merge conflicts feel scary, but they follow simple rules.

Conflicts usually happen when:

- the same lines are edited
- long-lived branches drift too far
- formatting tools rewrite files

The secret to fewer conflicts:

- pull frequently
- rebase small changes
- keep PRs small

When conflicts do happen:

- read both sides
- understand intent
- don’t blindly choose “ours” or “theirs”

Conflicts are not Git being bad.  
They are Git asking you to make a decision.

## 8. Git Remembers Everything (Even When You Think It’s Gone)

Deleted a commit?  
Reset too hard?  
Lost work?

Before panicking, remember this magic command:

```
git reflog
```

It shows:

- every HEAD movement
- every checkout
- every reset

Most “lost” commits are not lost.  
They’re just **not referenced anymore**.

Knowing `reflog` turns Git from scary to forgiving.