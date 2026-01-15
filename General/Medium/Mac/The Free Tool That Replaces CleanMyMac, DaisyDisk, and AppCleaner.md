
_A single free tool that cleans, uninstalls, analyzes disk usage, and monitors your Mac, without subscriptions, nags, or “upgrade” popups._

If you like your Mac fast, clean, and boring in the best possible way, you already know the problem. You install CleanMyMac to clear junk, DaisyDisk to understand where storage went, and AppCleaner to uninstall apps without leaving a trail of crumbs behind.

Mole is a free command line tool that brings that entire stack into one place, with an interactive menu and dedicated commands for cleaning, uninstalling, disk analysis, optimization, and live system status. Let’s see how.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:700/1*5UUf6dajjQNPDfGt7Il4tw.png)

Hi, this is Jannis.

Imagine that from now on you can run one tool in the terminal with a single command (`mo`) and you can get an interactive menu and specific commands to get all your maintenance jobs done in an instant. I will walk you through everything you need to know.

## What Mole replaces and how it maps to your current tools

Before we install and try out mole let’s understand what it covers and why it can replace your existing maintenance tooling.

If you are coming from CleanMyMac, you care about reclaiming space from caches, logs, temp files, and browser leftovers. Mole’s `mo clean` targets that category of cleanup and is explicitly positioned as “deep cleaning” that can reclaim gigabytes.

If you are coming from AppCleaner, you care about uninstalling an app and also removing the launch agents, preferences, caches, and the other hidden pieces that stay behind. Mole’s `mo uninstall` is designed for “apps plus leftovers” removal, not just deleting the `.app` bundle.

If you are coming from DaisyDisk, you care about understanding disk usage fast, finding large directories, and seeing what is actually consuming space. Mole’s `mo analyze` is described as a “visual disk explorer” and it sits next to cleanup and uninstall in the same tool.

Then there is a category you may be using iStat Menus for: live system stats. Mole includes `mo status`, a “live system health dashboard” for CPU, GPU, memory, disk, and network so you can diagnose performance issues without adding another utility.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:700/1*LG0RX4yXkI2_BH369LwQfA.gif)

cleanup, uninstall, and system status all sit behind one command you can run on autopilot.

## Time for the fun part (Installing mole)

Mole can be installed via Homebrew or an install script.

If you run Homebrew, do this brew:

brew install mole

Once it is installed, you have two practical ways to start.

You can drop into the interactive menu by just typing “mo”:

mo

Or you can go straight to the action you care about:

mo clean  
mo uninstall  
mo analyze  
mo status  
mo optimize

Those commands will “replace three apps”.

## Your new safe workflow

Cleanup tools have a reputation problem because a lot of them behave like a black box. You click a button, it deletes things, and you are not sure what changed until something breaks.

Mole instead calls out safety as a first class concern and explicitly points you to preview changes before doing them. The most important habit you can adopt is running with `--dry-run` first:

mo clean --dry-run

That gives you a preview of what it intends to remove before it touches anything.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:700/1*pwwD_uUicDEUAGLRRYjk1A.png)

The second habit I recommend is using the whitelist mechanism to protect paths and caches you never want touched:

mo clean --whitelist

There is a matching pattern for system optimization, which is where tools often cross the line from “cleanup” to “tuning”:

mo optimize --dry-run  
mo optimize --whitelist

Mole also includes a helper command to configure Touch ID for sudo actions:

mo touchid

Many cleanup and maintenance actions require elevated privileges, and Touch ID is a practical way to keep that secure and less annoying than repeated password prompts.

[

## Your Mac Ran Out of Storage 😳? This Is Why I Never Have To Worry Again

### Managing storage on a Mac can be a challenge, especially when dealing with large applications and data. I’ve explored a…

medium.com



](https://medium.com/@PowerUpSkills/your-mac-ran-out-of-storage-this-is-why-i-never-have-to-worry-again-b8cdd0682f35?source=post_page-----8dc8e069cb53---------------------------------------)

## Replace DaisyDisk with `mo analyze`

When your disk is close to full disk analyzers are useful because they show structure. You spot a surprise `node_modules` explosion, an old Xcode cache, or a downloads folder that turned into a landfill.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:700/1*T2jy6JHUoC5bpXC0GSNjWA.png)

Mole’s disk side lives in `mo analyze`, described as a visual disk explorer. From now on you run the analysis, you identify the big offenders, and then you decide whether they are candidates for cleanup, purge, or manual deletion.

The disk analysis component includes handling for large directories like `node_modules`. And these can stack up over time.

## Replace AppCleaner with `mo uninstall` when you want a real uninstall

Dragging an app to the trash is not an uninstall. You know it, and your `~/Library` knows it too.

Mole provides `mo uninstall` as a smart uninstaller that removes apps along with launch agents, preferences, and other remnants. That is the job AppCleaner does well, and it is also the job that tends to be the most annoying to do manually, because leftovers live in many different locations.

The best way to use Mole here is to treat uninstall as a two step operation. You scan what it plans to remove, then you confirm. If you are uninstalling anything that touches developer tooling, drivers, or network configuration, you should be extra carefull and lean on dry run previews and whitelists.

[

## Mac Terminal Aliases: 3 Lines That Can Save You Days

### The next time you catch yourself typing the same thing again and again, remember: you’re three lines away from saving…

medium.com



](https://medium.com/@PowerUpSkills/mac-terminal-aliases-3-lines-that-can-save-you-days-96f2c2ce9de7?source=post_page-----8dc8e069cb53---------------------------------------)

## Replace CleanMyMac with `mo clean` and `mo optimize`, but keep your standards high

Most people install CleanMyMac for one reason: you want to reclaim space and reduce sluggishness without turning Mac maintenance into a weekend project.

Mole’s `mo clean` focuses on deep cleanup across caches, logs, and browser leftovers. Its README even gives a sense of the kinds of buckets it scans, including browser caches, developer tools, and general system temp and log files.

Then there is `mo optimize`, which is positioned as refreshing caches and services, and release notes describe it as “full system tuning” including actions like cache refresh, network reset, log cleanup, and Spotlight rebuild.

In general you should keep a clear line between “cleanup” and “tuning.” Cleanup is usually low risk if you understand what is being removed. Tuning can be useful, but it can also change behavior in ways you notice later.

The better approach is to treat `mo optimize` like you treat any script that touches system services: preview first, then run intentionally, and only when you actually need it. Mole supports that workflow explicitly via `--dry-run` for optimization actions too.

[

## Scan and Remove macOS Junk Files With a Single Terminal Command

### A free open source tool that cleans caches, logs and temporary files without installing anything.

medium.com



](https://medium.com/@PowerUpSkills/scan-and-remove-macos-junk-files-with-a-single-terminal-command-63f95d51bdab?source=post_page-----8dc8e069cb53---------------------------------------)

## Use `mo status` as the “why is my Mac slow” dashboard

A lot of performance debugging is about identifying what is eating resources right now.

Mole includes a live monitoring dashboard via `mo status`, showing time stats across CPU, GPU, memory, disk, and network. Similar to well known iStat you may be using already.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:700/1*_jJP9aaxOHBQKajv93_Qww.png)

If you ever find yourself asking “is this slowdown memory pressure, disk I/O, or network”, a status dashboard you can pull up instantly in the terminal is exactly the kind of small capability that keeps you in flow.

## The developer specific feature you will appreciate more than you expect

Mole includes `mo purge`, which is described as cleaning project build artifacts.

If you build software, you know how quickly caches and build outputs can pile up across projects. This command is the bridge between “general Mac cleanup” and “developer machine hygiene,” and it is often the difference between a tool feeling generic versus feeling built for your workflow.

Mole also supports shell completion via `mo completion`, which is the kind of quality of life feature that makes a CLI tool feel native in daily use.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:700/1*FITVU9RkzB1OGdvaD7e5bA.png)

If you adopt one rule, make it this: always start with `--dry-run`, then let the tool earn permission to delete. When you do that, Mole becomes the kind of maintenance utility that stays out of your way and quietly keeps your Mac feeling sharp.

[

## GitHub - tw93/Mole: 🐹 Deep clean and optimize your Mac.

### 🐹 Deep clean and optimize your Mac. Contribute to tw93/Mole development by creating an account on GitHub.

github.com



](https://github.com/tw93/Mole?source=post_page-----8dc8e069cb53---------------------------------------)

If you found this article helpful, A few claps 👏, a highlight 🖍️, or a comment 💬 really helps.

If you hold that 👏 button down something magically will happen, Try it!

**Don’t forget to follow me to stay updated on my latest posts.** Together, we can continue to explore fascinating topics and expand our knowledge.

Thank you for your time and engagement!

[

Mac

](https://medium.com/tag/mac?source=post_page-----8dc8e069cb53---------------------------------------)

[

Macos

](https://medium.com/tag/macos?source=post_page-----8dc8e069cb53---------------------------------------)

[

Productivity Hacks

](https://medium.com/tag/productivity-hacks?source=post_page-----8dc8e069cb53---------------------------------------)

[

Productivity Tips

](https://medium.com/tag/productivity-tips?source=post_page-----8dc8e069cb53---------------------------------------)

[

Productivity Tools

](https://medium.com/tag/productivity-tools?source=post_page-----8dc8e069cb53---------------------------------------)

378

7

[

![Jannis](https://miro.medium.com/v2/resize:fill:96:96/1*AZ8boysRVFpu2_a7SzFXyA.png)



](https://medium.com/@PowerUpSkills?source=post_page---post_author_info--8dc8e069cb53---------------------------------------)

[

## Written by Jannis

](https://medium.com/@PowerUpSkills?source=post_page---post_author_info--8dc8e069cb53---------------------------------------)

[3.3K followers](https://medium.com/@PowerUpSkills/followers?source=post_page---post_author_info--8dc8e069cb53---------------------------------------)

·[6 following](https://medium.com/@PowerUpSkills/following?source=post_page---post_author_info--8dc8e069cb53---------------------------------------)

Product Owner in global telecom, lifelong tech tinkerer, and Mac user. Sharing hands-on hacks, real stories, and the tools that make work (and life) smarter.

Following