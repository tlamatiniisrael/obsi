
# ⚡ Git Like a Pro: 15 Commands That Instantly Level Up Your Dev Game

Towards Dev



![](https://towardsdev.com/?source=post_page---publication_nav-a648dc4ecb66-84bd57561b38---------------------------------------)

![](https://miro.medium.com/v2/resize:fit:1314/1*f1pAnKhN0TSUJBlSsqhaVA.png)

⚡ Git Like a Pro: 15 Commands That Instantly Level Up Your Dev Game

Member-only story

# ⚡ Git Like a Pro: 15 Commands That Instantly Level Up Your Dev Game

_Because knowing_ `_git add ._` _isn’t senior-level anymore._

## 🧠 Why You Should Care

If you’ve ever stared at a broken branch, cursed during a merge conflict, or accidentally force-pushed to `main` (we’ve all been there 😅), this post is for you.

These are the **15 Git power moves** senior engineers use daily — not just to _survive_ Git, but to make it their superpower.

## 🚀 1. `git reflog` — The “Undo Anything” Lifeline

Accidentally reset your branch? Lost commits after a rebase?  
👉 `**git reflog**` is your time machine.

```sh
git reflog  
git checkout HEAD@{3}
```

This command shows every change your HEAD has gone through — even the “oops” ones.

## 🧩 2. `git stash -p` — Save Only What Matters

Instead of stashing _everything_, interactively pick what to stash.

```sh
git stash -p
```

It lets you keep your workspace clean without losing _that one debug line_ you were testing.

## 🔍 3. `git bisect` — Debug Like Sherlock

Binary search your commit history to find which commit broke something.

git bisect start  
git bisect bad  
git bisect good v2.0

Git will walk you through commits until it pinpoints the bad one.  
Perfect for “It worked last week!” mysteries.

## ⚙️ 4. `git cherry-pick` — The Surgical Commit Extractor

Need just one commit from another branch? Don’t merge the whole thing.

git cherry-pick <commit-hash>

Instantly applies that commit to your current branch — clean and precise.

## 💣 5. `git commit --fixup` + `git rebase -i --autosquash`

Clean commit history, no manual squashing.

git commit --fixup <commit-hash>  
git rebase -i --autosquash

Result: professional, atomic commits your team will actually understand.

## 🧱 6. `git worktree` — Multiple Branches, One Repo

Ever needed two branches checked out at once? Use `worktree`.

git worktree add ../feature-xyz feature-xyz

No more cloning your repo twice — just multiple working trees from the same repo.

## 🧭 7. `git log --graph --oneline --decorate --all`

Visualize your branch structure beautifully.

git log --graph --oneline --decorate --all

You’ll instantly see merges, commits, and branching flow — great for team discussions.

## 🔐 8. `git clean -df` — Reset Your Workspace Safely

Remove untracked files and directories (great before builds).

git clean -df

Pro tip: use `-n` first to preview what’ll be deleted.

## 🔄 9. `git reset --soft HEAD~1` — Undo a Commit, Keep Changes

Perfect for “Oops, wrong commit message” moments.

git reset --soft HEAD~1

Your changes remain staged — just re-commit correctly.

## 🪄 10. `git shortlog -sn` — See Who’s Actually Contributing

Curious about commit stats?

git shortlog -sn

It lists contributors ranked by commit count — great for retros or bragging rights.

## 🧩 11. `git diff --staged`

See exactly what you’re about to commit.

git diff --staged

No more “Wait, I didn’t mean to commit that file!” moments.

## 🧵 12. `git blame -L`

Find _who_ last edited a specific line (and when).

git blame -L 50,60 src/App.java

Perfect for debugging legacy code or gently roasting teammates 😜.

## 📦 13. `git tag -a v1.0.0 -m "First release"`

Tag commits for clean versioning.

git tag -a v1.0.0 -m "First release"  
git push origin v1.0.0

Clean, semantically versioned tags make deployments a breeze.

## 🧰 14. `git config --global alias.co checkout`

Speed up your workflow with Git aliases.

git config --global alias.co checkout  
git config --global alias.br branch  
git config --global alias.cm "commit -m"

Now `git co main` feels like magic.

## 💥 15. `git rebase -i HEAD~5` — Rewrite History Like a Pro

Interactive rebasing gives you full control over your last few commits.

git rebase -i HEAD~5

Reword, squash, or drop commits — clean, intentional commit history = senior-level hygiene.

## 🧠 Final Thoughts

Git isn’t just a version control tool — it’s a **time machine**, **code historian**, and **debug assistant**.

Mastering these commands doesn’t just make you faster; it makes you _fearless_.  
You’ll commit, merge, and rebase with confidence — like a true senior engineer.

## 🔥 If You Liked This…

Drop a ❤️ or share it with your team.  
The next time someone says “I broke the branch,” just send them this article 😉