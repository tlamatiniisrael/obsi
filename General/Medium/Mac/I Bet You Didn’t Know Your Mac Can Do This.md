
Your Mac has been hiding a secret from you. Right now, buried in your Terminal, there’s a command that can transform your development workflow.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/1*-_PWfJYFVdMfgOU-YutK9A.png)

I will show you how to turn your computer into a surprisingly capable voice assistant.

## “Wait, That Actually Works?”

Open your Terminal right now and type this:

```sh
say "Hello, I'm your Mac speaking to you"
```

Go ahead, I’ll wait.

Did your jaw drop a little? Mine did when I first discovered this. Your Mac just spoke to you using a built-in, fully-featured text-to-speech system that most developers never know exists. No external libraries, no API calls, no dependencies — it’s just… there.

That simple command you just ran is actually the tip of an iceberg that goes much deeper than you’d expect.

## The Voice Collection You Never Knew You Had

Your Mac doesn’t just have one voice — it has an entire cast of characters waiting to be discovered. Try this:

```sh
say -v "?"
```

Surprise! Your Mac comes loaded with over 20 different voices, each with its own personality:

**Professional Voices:**

- `Alex` – The default, most natural-sounding voice
- `Samantha` – Clear, professional female voice
- `Daniel` – British English male voice
- `Karen` – Australian English female voice

**Character Voices:**

- `Zarvox` – Robot voice perfect for sci-fi projects
- `Princess` – Whimsical, fairytale-like voice
- `Bad News` – Ominous, dramatic delivery
- `Boing` – Cartoon-like, bouncy voice
- `Bubbles` – Underwater, gurgling effect

Want to hear them in action?

```sh
say -v "Zarvox" "I am a robot from the future"  
say -v "Princess" "Welcome to the magical kingdom of code"  
say -v "Bad News" "Your deployment has failed"
```

Each voice has been carefully crafted and some are surprisingly high-quality.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/1*rF-T09yMJbArC26H9TQCvA.png)

## Practical Ideas To Use The “say” Command

Do I have your attention? good. Let’s do some serious stuff now and use the voice feature to our advantage.

## 1. Build Process Notifications

Ever started a long build process and then forgot about it while browsing Reddit? Add audio notifications to your build scripts:

```sh
#!/bin/bash  
echo "Starting build process..."  
npm run build  
  
if [ $? -eq 0 ]; then  
    say -v "Samantha" "Build completed successfully"  
else  
    say -v "Bad News" "Build failed. Check your console."  
fi
```

## 2. Test Suite Audio Feedback

Make your test results impossible to ignore:

```sh
# In your package.json or test script  
npm test && say "All tests passing" || say -v "Bad News" "Tests are failing"
```

## 3. Deployment Confirmations

Add an extra layer of safety to your deployment scripts:

```sh
deploy_to_production() {  
    say -v "Alex" "About to deploy to production. Press Enter to continue or Control-C to cancel."  
    read -p ""  
    # deployment logic here  
    say -v "Princess" "Production deployment complete!"  
}
```

## 4. Code Review Reminders

Set up periodic reminders for pending code reviews. To activate this, you need to:  
1. Open your terminal.  
2. Run `crontab -e` to edit your crontab.  
3. Paste the line:

```sh
# Add to your crontab  
0 */2 * * * say -v "Karen" "Don't forget to check your pending code reviews"
```

This is a cron job entry, intended to be added to your crontab (the schedule table for the cron daemon on Unix-like systems like your Mac). It automates running commands at specified times and intervals. This job runs every 2 hours, on the hour (e.g., 00:00, 02:00, 04:00, …, 22:00), every day reminding you via Karen’s Voice to check your code review. Nice!

## Advanced Integration Techniques

But wait there is even more. Let’s integrate the say command seamlessly with our existing development tools and workflows.

## Script Integration with Variables

The `say` command handles dynamic content beautifully:

```sh
#!/bin/bash  
BRANCH=$(git branch --show-current)  
COMMIT_COUNT=$(git rev-list --count HEAD)  
  
say -v "Alex" "Currently on branch $BRANCH with $COMMIT_COUNT commits"
```

## File-Based Speech

You can make your Mac read entire files:

```sh
# Read your README aloud while you work on something else  
say -f README.md
```

```sh
# Or create a daily standup script  
echo "Today's tasks: Review pull requests, fix authentication bug, update documentation" > daily_tasks.txt  
say -f daily_tasks.txt -v "Samantha"
```

## Speed and Pitch Control

Fine-tune the speech output for different contexts:

```sh
# Slow down for complex information  
say -r 150 "Deploying to production server with SSL certificates"
```

```sh
# Speed up for quick notifications  
say -r 300 "Build complete"
```

## Audio File Generation

This one is great for coding your own voice playing Applications. Create reusable audio notifications:

```sh
# Generate audio files for common notifications  
say -v "Alex" "Build successful" -o build_success.aiff  
say -v "Bad News" "Critical error detected" -o error_alert.aiff
```

```sh
# Play them instantly when needed  
afplay build_success.aiff
```

## Integration with Popular Development Tools

## Git Hooks Integration

Add the `say` command to your Git hooks for audio feedback on repository actions:

```sh
# .git/hooks/pre-commit  
#!/bin/bash  
say -v "Karen" "Running pre-commit checks"
```

```sh
# .git/hooks/post-commit  
#!/bin/bash  
COMMIT_MSG=$(git log -1 --pretty=%B)  
say -v "Alex" "Commit successful: $COMMIT_MSG"
```

## Docker and Container Workflows

Monitor long-running container operations:

```sh
# In your Docker scripts  
docker build -t myapp . && say "Docker build complete" || say -v "Bad News" "Docker build failed"
```

```sh
# Container health checks  
docker ps --filter "status=running" --format "table {{.Names}}" | grep -q myapp  
if [ $? -eq 0 ]; then  
    say -v "Princess" "All containers are healthy"  
else  
    say -v "Bad News" "Container health check failed"  
fi
```

## Monitoring and Alerting

Create sophisticated monitoring scripts:

```sh
#!/bin/bash  
# Server health check with audio alerts  
check_server_health() {  
    response=$(curl -s -o /dev/null -w "%{http_code}" https://your-api.com/health)  
      
    if [ $response -eq 200 ]; then  
        say -v "Samantha" "Server health check passed"  
    else  
        say -v "Bad News" "Server health check failed with status code $response"  
    fi  
}
```

## Pro Tips That Will Change Your Workflow

## 1. Create Voice Aliases

We can control the Voice character based on the scenarios we want to be notified. Set up aliases for different types of notifications:

```sh
# Add to your .bashrc or .zshrc  
alias say-success='say -v "Princess" -r 200'  
alias say-error='say -v "Bad News" -r 180'  
alias say-info='say -v "Alex" -r 220'
```

```sh
# Usage  
npm install && say-success "Dependencies installed successfully"
```

## 2. Context-Aware Notifications

Make your notifications smarter by reading your environment:

```sh
#!/bin/bash  
if [ -f package.json ]; then  
    PROJECT_NAME=$(cat package.json | grep '"name"' | cut -d'"' -f4)  
    say -v "Alex" "Working on $PROJECT_NAME project"  
fi
```

## 3. Multi-Language Support

The `say` command supports multiple languages and accents:

```sh
say -v "Thomas" "Bonjour, votre build est terminé"  # French  
say -v "Yuki" "こんにちは、ビルドが完了しました"     # Japanese
```

Let’s think our use cases even further. What if we integrate our say command with the broader macOS ecosystem:

## Shortcuts App Integration

You can trigger `say` commands through Siri Shortcuts, creating voice-activated development commands:

```sh
# Create a shortcut that runs: say -v "Alex" "Starting development environment"  
# Trigger with: "Hey Siri, start dev mode"
```

## AppleScript Integration

Combine with AppleScript for even more sophisticated automation:

```sh
tell application "Terminal"  
    do script "say -v 'Princess' 'Code review reminder'"  
end tell
```

## And We Just Started…

In an era where developers juggle multiple terminals, browser tabs, and applications, audio feedback creates a new dimension of awareness.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/1*Bbddb0aIE2J6SzjhjYkx2A.png)

Your Mac has been capable of speech synthesis this entire time, with a sophisticated system. I think there is so much you can do with it.

**If you found this article helpful, A few claps 👏, a highlight 🖍️, or a comment 💬 really helps.**

If you hold that 👏 button down something ==magically will happen==, Try it!

**Don’t forget to follow me to stay updated on my latest posts.** Together, we can continue to explore fascinating topics and expand our knowledge.

Thank you for your time and engagement!