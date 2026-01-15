
If you’re a developer, you need to be using Git. It’s an essential tool for version control and team collaboration. But sometimes you’re working on a feature or fixing a bug, and you need to switch branches or take care of something urgent. That’s where Git Stash comes into play.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:700/1*6AVZB9k7DPMyFaLoKqenPg.png)

Git stash basic commands

## What is Git Stash?

The git stash command lets you stash (store) your code so you can save it in a temporary directory and then come back to it later. You can use it when you want to switch branches or pull updates from a remote repository without committing your work in progress.

Whenever you stash your changes, Git saves them in a kind of stack-like structure. That way, you can retrieve them later. Stashing is a great way to keep your working directory clean while continuing your progressive work.

## Why Use Git Stash?

Here are some scenarios where git stash can be a helpful:

- Switching Branches: You’re working on a branch and you need to switch to another to fix a bug, bisecting or reviewing a pull request.
- Pulling Changes: You want to pull the latest changes from a remote repository, but your working directory still has changes to be committed.
- Saving Work-in-Progress: You’re not ready to commit your changes but need to save them temporarily to do another task instead.
- Cleaning Up: You want to clean up your working directory without losing your current progress.

## How Git Stash Works

When you use git stash, Git would do the following:

- I would save your uncommitted changes (both staged and unstaged) in a stash container.
- It reverts your working directory to match the last commit (HEAD), effectively cleaning it up.
- The stashed changes are stored in an ordered stack, so you can reapply them later on.

## Basic Git Stash Commands

Here are the most commonly used git stash commands:

### 1- Stash Changes

To stash your changes, run:

´´´ 
git stash
´´´

This command stashes both staged and unstaged changes. If you want to include untracked files (new files that haven’t been added to Git), use:

git stash -u

Or, for untracked and ignored files:

git stash -a

### 2- List Stashes

To view all stashes, use:

git stash list

This will display a list of stashes with identifiers like stash@{0}, stash@{1}, etc.

### 3- Apply Stash

To reapply the most recent stash, run:

git stash apply

If you want to apply a specific stash, specify its identifier:

git stash apply stash@{2}

### 4- Pop Stash

git stash pop applies the most recent stash and removes it from the stash stack:

git stash pop

### 5- Drop Stash

To delete a specific stash, use:

git stash drop stash@{1}

### 6- Clear Stash

To delete all stashes, run:

git stash clear

## Advanced Git Stash Features

### 1- Stashing with a Message

You can add a description to your stash for better organization:

git stash save "Added UserController to the users route in the api.php"

### 2- Stashing Specific Files

If you only want to stash specific files, use:

git stash push path_to_fileX path_to_fileY

If you only want to stash specific files including new untracked files, use:

git stash push path_to_fileX path_to_fileY

### 3- Creating a Branch from a Stash

If you want to create a new branch from a stash, run:

git stash branch new-branch-name stash@{1}

This creates a new branch and applies the stash to it.

### 4- Partial Stash

You can stash only staged or unstaged changes using:

git stash --keep-index  #Stash unstaged changes, keep staged changes

git stash --patch  #Interactively choose which changes to stash

## Regular Workflow with Git Stash

Here’s a typical workflow for using git stash in your development process:

### 1- Start working on your feature

You’re working on a new feature on feature-branch and making progress.

git checkout feature-branch  #You make some changes in a few files.

### 2- Realize you need to switch to main to fix a bug

But you don’t want to commit your half-done work yet, and you need to temporarily store these changes.

### 3- Stash your changes

Before switching branches, stash the changes to save your work:

git stash

This saves your changes to a “stash” and resets the working directory.

### 4- Switch to the bug-fix branch

Now you can safely switch to another branch without your changes affecting it:

git checkout main  #You're now on the main branch, ready to fix the bug.

### 5- Make the necessary bug fix

You fix the bug, make the changes, and commit them:

git add .  
git commit -m "Changed primary button's color"

### 6- Return to your original work

Once the bug fix is done, return to the feature branch:

git checkout feature-branch

### 7- Apply your stashed changes

Now, bring your stashed changes back into your working directory:

git stash pop

This will apply the changes you stashed earlier. If you want to keep the stashed changes for later use, you can use git:

git stash apply

Instead of **git stash pop**, but pop removes the stash after applying it and no longer exists in the stash list.

### 8- Continue working on your feature

Now you can continue where you left:

git add .  
git commit -m "Finished with feature branch dev"

If you have multiple stashes and want to see what’s stored, use:

git stash list

If you’re done with your stashes and want to clean things up:

git stash clear

## Best Practices for Using Git Stash

- **Use Descriptive Messages**: Always add a message to your stashes to make them easier to identify later.
- **Avoid Overusing Stash**: Stashing is great for short-term storage. For long-term work, consider creating a branch instead.
- **Review Stashes Regularly**: Periodically review and clean up your stash stack to avoid clutter.
- **Combine with Other Git Features**: Use git stash in combination with git rebase, git cherry-pick, or git merge for more advanced workflows.
- **Conflicts When Applying Stashes**: If applying a stash causes conflicts, resolve them manually and commit the changes.
- **Stashing Large Changes**: Avoid stashing large changes, as this can slow Git operations down the road.

## In a nutshell

Git Stash is a handy tool that helps you manage your work efficiently. By learning to use it effectively, you can keep your workspace clean, switch branches smoothly, and avoid unnecessary commits. If you use Git Stash as part of your regular Git workflow, you’ll find it becomes an indispensable part of your development toolkit.