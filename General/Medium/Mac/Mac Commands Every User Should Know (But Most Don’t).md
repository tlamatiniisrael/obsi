
## Why These Hidden Tools Make Your Mac 10× More Powerful

Hey Mate! I am actually a Windows lover before buying the Mac M3. After that really wondered. As a DevOps engineer, I have very cool features, like using Linux for daily life—no more WSL and VM box.

### Mac DevOps-friendly. Is that right?

But here we see all the useful commands for all types of users, not only DevOps or Developers.  
**If you know your Mac’s secret commands, you basically unlock god-mode.**

So here’s the ultimate, simple, real-world guide to Mac terminal commands that every techie should have in their backpack — whether you’re a developer, sysadmin, or the cool friend people call when their WiFi stops working.

### 1 Make Your Mac Speak

When your eyes are tired but you still need to “read” something:

```sh
say "Hello, world"
```

Your Mac literally reads out the text. Super useful when debugging long logs or proofreading.

### 2 Find or Copy Your WiFi Password

Ever forget your WiFi password but your Mac still knows it? Yup — it’s sitting in your keychain.

### 3 View WiFi Password

security find-generic-password -wa "<wifi-SSID-name>"

### 4 ==Copy WiFi Password to Clipboard==

security find-generic-password -wa "4G-wifi-Tukzer" | pbcopy

### 5 Keep Your Mac Awake (No More Auto-Sleep)

Perfect for long installs, backups, downloads, or server jobs.

caffeinate

Your Mac stays awake until you stop the command.

## 6 Screenshot Shortcuts Everyone Should Know

Quick, clean, and super handy.

`CMD + SHIFT + 4` Save screenshot to Desktop

`CMD + SHIFT + CTRL + 4` Save screenshot to Clipboard

### 7 Change Screenshot File Type

Set JPG instead of PNG:

defaults write com.apple.screencapture type jpg

### 8 Change Screenshot Name

defaults write com.apple.screencapture name "kv_pic"

### 9 Set Custom Screenshot Save Location

defaults write com.apple.screencapture location ~/Desktop/screenshot

## 10 Check Your Download History

Useful for audits or figuring out “where did that .dmg come from?”.

sqlite3 ~/Library/Preferences/com.apple.LaunchServices.QuarantineEventsV* \  
'select LSQuarantineDataURLString from LSQuarantineEvent'

## 11 Change Your Mac Login Keychain Password

If your login password changes and Keychain starts crying:

security set-keychain-password

## 12 Open Any File from Terminal

Instead of right-click → Open:

open <filename>

## 13 Check Network Interface Details

ifconfig en1

Great for IP, MAC address, and network troubleshooting.

## 14 Switch Between Shells

Old-school or new-school? Pick your vibe.

zsh

## 15 Flush DNS Cache (Fix weird network issues)

If websites aren’t resolving properly, do a quick DNS reset:

sudo killall -HUP mDNSResponder; \  
sudo killall -HUP mDNSResponderHelper; \  
sudo dscacheutil -flushcache

## 16 Set a Terminal Alarm

Need a reminder while deep in work?

leave 0220

It will alert you at the specified time.  
Example output:  
`Alarm set for Sun Nov 3 02:20:00 IST 2024.`

## 17 Print Fancy Text in Terminal

For fun scripts or banners:

toilet <TEX>

![](https://miro.medium.com/v2/1*XGJvaGHBjV97UElM9XuhfA.png)

## 18 Play Tetris in Terminal

Yes… this is real. Enjoy:

tetris

## 19 Shutdown & Restart Commands

Quick control when GUI hangs.

### Shutdown now:

shutdown -h now

### Restart now:

shutdown -r now

## 20 Enable TouchID for Terminal (Sudo Without Typing Password)

This is one of the coolest tricks.  
You can use TouchID for sudo commands.

Open the sudo config:

sudo nano /etc/pam.d/sudo

Add this at the top:

auth       sufficient     pam_tid.so

Boom. Now you can run sudo with just your finger.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/1*fK-vFS8R27Eo2p6bF0XJWQ.png)

## 21 Auto-Run Any Script When You Log In (LaunchAgent)

You can make your Mac run scripts automatically at login — backups, sync jobs, cleanup, services, anything.

### Step 1: Create a .plist file

nano ~/Library/LaunchAgents/com.user.startup.plist

### Paste this:

<?xml version="1.0" encoding="UTF-8"?>  
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"  
 "http://www.apple.com/DTDs/PropertyList-1.0.dtd">  
<plist version="1.0">  
 <dict>  
   <key>Label</key>  
   <string>com.user.startup</string>  
   <key>ProgramArguments</key>  
   <array>  
     <string>/usr/local/bin/bash</string>  
     <string>/Users/you/scripts/startup.sh</string>  
   </array>  
   <key>RunAtLoad</key>  
   <true/>  
 </dict>  
</plist>

### Load the agent:

launchctl load ~/Library/LaunchAgents/com.user.startup.plist

Now your script fires every time you log in.

## 22 Schedule Cron Jobs Like a Server

Yes, macOS still supports cron.

### Open cron editor:

crontab -e

### Example: Clear Downloads every Sunday 2 AM:

0 2 * * 0 find ~/Downloads -type f -mtime +7 -delete

## 23 Use `automator` to Batch-Rename Files Automatically

Example workflow:  
→ Watch a folder  
→ Rename files (lowercase, add dates, replace spaces)  
→ Move them to another folder

Terminal trick to open Automator fast:

open -a Automator

This is clutch for people dealing with logs, screenshots, photos, etc.

## 24 Auto-Compress Screenshots (80% smaller!)

Screenshot PNGs are huge.  
Use this to shrink them **instantly after saving**.

Install ImageMagick:

brew install imagemagick

Create a watcher in Automator:  
— Folder Action → Desktop  
— Run Shell Script:

for f in "$@"  
do  
  mogrify -quality 70 "$f"  
done

Boom. Every new screenshot becomes small and lightweight.

## 25 Use `shortcuts` CLI to Trigger Mac Shortcuts

macOS now includes `shortcuts` automation.

### List all shortcuts:

shortcuts list

### Run a shortcut:

shortcuts run "My Shortcut"

You can trigger HomeKit, app actions, workflows, literally anything.

## 26 Auto-Mount Remote Servers When Connected to WiFi

Useful for office networks.

### Create a script:

#!/bin/bash  
SSID=$(networksetup -getairportnetwork en0 | cut -d':' -f2| xargs)  
if [[ "$SSID" == "Office-WiFi" ]]; then  
    open "smb://username:password@server/share"  
fi

### Run it using LaunchAgent every minute.

Your shared drives appear automatically. No clicks needed.

## 27 Auto-Kill Annoying Background Apps

Example: Sometimes Slack, Chrome helpers, and other stuff overload RAM.

Create auto-kill script:

#!/bin/bash  
killall "Google Chrome Helper"  
killall "Slack Helper"

Run via cron every hour.

## 28 Change System Volume from Terminal (Automation Scripts)

osascript -e "set volume output volume 60"

Great for work mode vs. Netflix mode.

## 29 Auto-Backup Important Files to iCloud / External Drive

### Sync folder to iCloud:

rsync -avh ~/Documents/important-files/ ~/Library/Mobile\ Documents/com~apple~CloudDocs/backup/

## 30 Sync to external drive:

rsync -avh ~/projects/ /Volumes/BackupDrive/projects/

Use cron or LaunchAgent to automate daily backups.

## 31 Run AppleScript from Terminal

You can control apps like Finder, Chrome, Safari, Notes, etc.

Example: Create a new note:

osascript -e 'tell application "Notes" to make new note with properties {name:"Todo", body:"Finish deployment script"}'

Insane level automation.

## 32 Auto-Lock Your Mac After Commands

Good for secure environments.

pmset displaysleepnow

Lock instantly:

osascript -e 'tell application "System Events" to keystroke "q" using {control down, command down}'

## 33 Create Custom Terminal Alerts (Better Than `leave`)

Get macOS notification pop-ups from terminal:

osascript -e 'display notification "Backup completed!" with title "Automation"'

Use this in scripts → instant visual alerts.

Your Mac isn’t just a laptop — it’s a powerful tool that becomes even more deadly once you know its hidden commands. These aren’t just “cool tricks”… they save real time, prevent real mistakes, and make you feel like you’re actually in control of your system.