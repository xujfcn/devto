---
title: 'Claude Code Installation Guide for Windows: Git, PATH, Environment Variables, PowerShell, WSL, and Full Troubleshooting (2026)'
published: true
description: 'A very detailed Windows guide to installing Claude Code for beginners. Covers PowerShell, winget, Git for Windows, Node.js, PATH, environment variables, command prompt vs PowerShell vs WSL, and common post-install problems.'
tags: 'ai, productivity, cli, tutorial'
canonical_url: null
cover_image: null
id: 3366102
date: '2026-03-18T06:18:46Z'
---

# Claude Code Installation Guide for Windows: Git, PATH, Environment Variables, PowerShell, WSL, and Full Troubleshooting (2026)

Windows users usually hit a different set of Claude Code problems than macOS users.

Not because Claude Code is impossible on Windows, but because Windows has more environment combinations:

- Command Prompt
- PowerShell
- Windows Terminal
- Git Bash
- WSL
- Node installed from MSI
- Node installed from winget
- Git installed but not added to PATH
- environment variables set for one shell but not another

That is why this guide goes slower and explains the setup chain from zero.

## Before You Install Anything: Choose Your Windows Path

There are two realistic paths.

### Option A: Native Windows setup

Use:
- Windows Terminal
- PowerShell
- Git for Windows
- Node.js for Windows
- npm global install

This is easier for most beginners.

### Option B: WSL setup

Use:
- WSL2
- Ubuntu or Debian inside WSL
- Linux install steps inside WSL

This is often cleaner for developers, but it adds an extra layer. If you are completely new, start with native Windows first unless you already use WSL.

## Recommended Beginner Setup

For most new users, I recommend:

- Windows 11 or updated Windows 10
- Windows Terminal
- PowerShell
- winget for package installs
- Git for Windows
- Node.js LTS
- Claude Code via npm

## Step 1: Open the Right Terminal

Install or launch **Windows Terminal** if possible.

Then open **PowerShell**.

Check what shell you are in:

```powershell
$PSVersionTable.PSVersion
```

If PowerShell opens and works, stay there for the whole setup. Do not mix PowerShell, cmd, Git Bash, and WSL during the initial install unless you know why.

## Step 2: Check Whether `winget` Is Available

`winget` is the easiest way to install Git and Node on modern Windows.

```powershell
winget --version
```

If it works, great.

If it does not:
- update App Installer from Microsoft Store
- or install packages manually from official websites

## Step 3: Install Git

Check first:

```powershell
git --version
```

If Git is missing, install it with winget:

```powershell
winget install --id Git.Git -e --source winget
```

Then **close and reopen PowerShell**.

Verify:

```powershell
git --version
where.exe git
```

### Why Git matters on Windows too

Same reason as on macOS:
- repo-aware workflow
- diffs
- safer edits
- version rollback
- many developer tools assume Git

If you do not have a repo yet:

```powershell
mkdir $HOME\Projects\claude-code-test -Force
cd $HOME\Projects\claude-code-test
git init
```

Optional global identity:

```powershell
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

## Step 4: Install Node.js and npm

Check existing versions:

```powershell
node --version
npm --version
```

If missing, install Node.js:

```powershell
winget install --id OpenJS.NodeJS.LTS -e --source winget
```

Close and reopen PowerShell again.

Verify:

```powershell
node --version
npm --version
where.exe node
where.exe npm
```

## Step 5: Install Claude Code

Check whether it already exists:

```powershell
where.exe claude
claude --version
```

If not, install it globally:

```powershell
npm install -g @anthropic-ai/claude-code
```

Then verify:

```powershell
where.exe claude
claude --version
```

## Step 6: Fix PATH Problems on Windows

The most common Windows complaint is:

- npm install says success
- but `claude` is still not recognized

Typical error:

```powershell
claude : The term 'claude' is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### First find npm's global prefix

```powershell
npm config get prefix
```

Usually this points to a directory whose `bin` or executable location should be discoverable through PATH.

Check where npm installed `claude`:

```powershell
npm list -g --depth=0
```

Also try:

```powershell
Get-Command claude -ErrorAction SilentlyContinue
```

### Common fix: reopen the shell

A lot of PATH issues are just stale sessions. Close PowerShell fully, open a new one, and try again.

### If PATH is still wrong

Inspect the user PATH:

```powershell
[Environment]::GetEnvironmentVariable("Path", "User")
```

And machine PATH:

```powershell
[Environment]::GetEnvironmentVariable("Path", "Machine")
```

If the npm global executable location is missing, you can add it through the Windows Environment Variables UI or with PowerShell.

Be careful editing PATH programmatically. Back it up first.

## Step 7: Set Environment Variables Correctly

Windows users often set variables in one place and assume every shell will see them. That is not always true.

### Session-only variable in PowerShell

```powershell
$env:ANTHROPIC_API_KEY = "your_key_here"
$env:OPENAI_API_KEY = "your_crazyrouter_key"
$env:OPENAI_BASE_URL = "https://crazyrouter.com/v1"
```

These only last for the current session.

### Persistent user-level environment variables

```powershell
[Environment]::SetEnvironmentVariable("ANTHROPIC_API_KEY", "your_key_here", "User")
[Environment]::SetEnvironmentVariable("OPENAI_API_KEY", "your_crazyrouter_key", "User")
[Environment]::SetEnvironmentVariable("OPENAI_BASE_URL", "https://crazyrouter.com/v1", "User")
```

Then close and reopen PowerShell.

Verify:

```powershell
echo $env:ANTHROPIC_API_KEY
echo $env:OPENAI_API_KEY
echo $env:OPENAI_BASE_URL
```

## Step 8: Understand the Difference Between PowerShell, cmd, Git Bash, and WSL

This matters because variables and PATH can behave differently.

| Environment | Good for Beginners? | Notes |
|------------|---------------------|-------|
| PowerShell | Yes | Best native Windows choice |
| Command Prompt | Okay | Less convenient than PowerShell |
| Git Bash | Mixed | Works, but adds another shell layer |
| WSL | Good for developers | Best if you want Linux-like behavior |

If you installed Claude Code in native Windows PowerShell, do not test it first in WSL and assume the same environment applies.

WSL has its own package system, paths, shell files, and variables.

## Step 9: Optional WSL Path

If you want the cleanest long-term developer environment on Windows, install WSL.

Check WSL:

```powershell
wsl --status
```

Install if needed:

```powershell
wsl --install
```

Then reboot if Windows asks.

After that, open Ubuntu and treat it like a Linux machine:

- install Git inside WSL
- install Node inside WSL
- install Claude Code inside WSL
- set environment variables inside WSL shell files

Do not assume your Windows-side Node install automatically covers WSL.

## Step 10: Verify Everything End-to-End

Run these checks:

```powershell
git --version
node --version
npm --version
claude --version
where.exe git
where.exe node
where.exe npm
where.exe claude
```

Then create a safe test folder:

```powershell
mkdir $HOME\Projects\claude-code-test -Force
cd $HOME\Projects\claude-code-test
if (-not (Test-Path .git)) { git init }
"# test" | Out-File README.md -Encoding utf8
```

And test the CLI with non-destructive commands first.

## Common Windows Problems and Fixes

## 1. `claude` is not recognized

Cause:
- npm global executable directory not in PATH
- shell not restarted
- install failed

Fix:
- reopen PowerShell
- check `npm config get prefix`
- confirm package exists in global npm list
- inspect PATH

## 2. Git installs but PowerShell still cannot find it

Cause:
- terminal session opened before install
- PATH not refreshed

Fix:
- fully close and reopen terminal
- verify with `where.exe git`

## 3. Node installs, but npm is missing or broken

Cause:
- incomplete install
- conflicting old Node version

Fix:
- uninstall conflicting Node versions if necessary
- reinstall LTS cleanly
- verify both `node --version` and `npm --version`

## 4. Environment variable is set in PowerShell but not in another terminal

Cause:
- variable was session-only

Fix:
- use persistent user-level environment variables
- reopen terminal after setting them

## 5. WSL works but PowerShell does not, or vice versa

Cause:
- you set up two different environments

Fix:
- decide whether you want native Windows or WSL as your main Claude Code environment
- complete setup inside that environment fully

## 6. Corporate proxy blocks npm install

You may need:

```powershell
npm config set proxy http://proxy.example.com:8080
npm config set https-proxy http://proxy.example.com:8080
```

And possibly session variables too.

## 7. Antivirus or security software interferes

Sometimes security tools interfere with freshly installed CLI tools or scripts.

If install logs look normal but executables do not behave normally, test in a clean terminal, confirm the file exists, and check Windows Security or endpoint protection history.

## A Safe Default Windows Setup

If you want the simplest path that is easiest to support, use this exact stack:

- Windows Terminal
- PowerShell
- winget
- Git for Windows
- Node.js LTS
- Claude Code via npm global install
- persistent user environment variables

That setup is boring, and that is exactly why it is good.

## FAQ

### Should beginners use PowerShell or WSL for Claude Code?

If you are new, start with PowerShell. If you already prefer Linux tooling or already use WSL daily, WSL may be cleaner long term.

### Why did Claude Code install successfully but still not run?

Most often: stale PATH, wrong shell, or npm installed the package into a location your current terminal is not reading.

### Do I need Git before using Claude Code on Windows?

For serious use, yes. Even if the CLI starts without Git, normal coding workflows are much smoother with Git installed and configured.

### Where should I store Claude Code environment variables on Windows?

For persistence, set them at the user environment level, not just the current shell session.

### Is Git Bash a good place to run Claude Code?

It can work, but for beginners it adds more variables. PowerShell is simpler to document and support.

## Final Take

The Windows installation story is not hard because Claude Code itself is hard. It is hard because Windows gives you many overlapping environments.

If you keep the setup consistent — PowerShell, winget, Git, Node, npm, Claude Code, then environment variables — the install becomes much easier to debug and teach.
