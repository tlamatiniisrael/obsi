
![](https://miro.medium.com/v2/resize:fit:1400/1*bPqBkLLomsUohQJmSNngvQ.png)

Two years ago, my nickname on the team Slack was “Force-Push Freddy.”  
I once accidentally deleted the entire release branch two hours before launch. The 3 AM hotfix call still haunts me. Fast-forward to November 2025: I’m the one who rescues broken releases in 7 minutes flat, writes the Git guides, and gets tagged whenever history looks scary. The difference? Ten small Git habits that feel like superpowers.

## Framework: Work Like Nothing Can Go Wrong (Because It Won’t)

1. **Never lose work**
2. **Never break main**
3. **Never waste time hunting**

Master these three and you’re untouchable.

## The 10 Tricks That Changed Everything

## 1. `git reflog` — Your Personal Time Machine

Lost a branch? Force-pushed over something important? `git reflog` shows every place your HEAD has ever been. I recovered a week of work in 30 seconds after a bad rebase. Keep this in your muscle memory.

## 2. `git worktree` — Multiple Branches, One Repo

No more stashing just to check another branch. `git worktree add ../hotfix-123 hotfix-123` gives you a second checkout. I run bug fixes and feature work side-by-side. Zero context switching pain.

## 3. `git commit --amend --no-edit` + `git push --force-with-lease`

The safe force-push. `--force-with-lease` refuses if someone pushed since your last fetch. I fix typos and lint errors without ever breaking anyone again.

## 4. `git restore --staged <file>` — Unstage in One Second

Accidentally added your IDE settings? This is faster than reset and won’t touch your working tree. My PRs went from 15-minute cleanups to instant.

## 5. `git switch -c new-branch` — Create and Checkout in One Command

No more `checkout -b`. Cleaner, clearer, and I never forget the `-b` again.

## 6. `git config --global core.excludesfile ~/.gitignore_global`

One global file for `*.idea`, `*.iml`, `target/`, etc. I stopped committing junk across every repo on day one of using this.

## 7. `git add -p` — The Real MVP

Add only the hunks you want. I split huge refactors into perfect, logical commits while reviewers cheer. PRs went from “please split” to “perfect atomic commits.”

## 8. `git rebase -i --autosquash` + Conventional Commits

Write `fixup! Commit title` commits, then `git rebase -i --autosquash` cleans everything automatically. My history is now spotless with almost zero effort.

## 9. `git fetch && git reset --hard origin/main` — The Nuclear Emergency Button

When main is broken and you just need to nuke your local mess. I saved a junior dev’s laptop in 8 seconds during onboarding.

## 10. `git config --global alias.graph "log --oneline --graph --decorate --all"`

Run `git graph` and instantly see what’s going on. I debug merge conflicts before breakfast.

## Real Impact

2023 → 2025 comparison:

- Force-push incidents: 27 → 0
- Time wasted on bad history: 40 hours/month → <2 hours
- PR review time for my commits: 18 min → 6 min (reviewers actually thank me)
- On-call incidents caused by Git mistakes: 6 → 0

## Why This Matters in 2025

With trunk-based development, monorepos, and 100+ PRs a day, Git fluency isn’t nice-to-have — it’s survival. The best engineers I know aren’t smarter; they just never lose work and never break the build.

## A Personal Reflection

That 3 AM call was rock bottom. But Git didn’t break me — it taught me. These ten tricks aren’t commands; they’re **insurance policies**. Insurance against embarrassment, against lost work, against being “that guy.” Now when someone pings me at midnight with “I broke everything,” I smile. Because I know it’ll be fixed before coffee.

## Conclusion

You don’t need to be a Git wizard. You just need these 10 tricks. Pick one today — start with `reflog` or `add -p` — and feel the difference tomorrow. Which trick are you trying first? Drop it below. I still reply to every comment (and I’ll probably learn trick #11 from you).