---
title: 'Claude Code Installation Guide for macOS: Git, Environment Variables, PATH, and Every Common Fix (2026)'
published: true
description: 'A beginner-friendly macOS guide to installing Claude Code the right way. Covers Homebrew, Node.js, Git, shell setup, PATH fixes, environment variables, permission issues, Apple Silicon vs Intel, and post-install troubleshooting.'
tags: 'ai, productivity, cli, tutorial'
canonical_url: null
cover_image: null
id: 3366103
date: '2026-03-18T06:18:46Z'
---

# Claude Code Installation Guide for macOS: Git, Environment Variables, PATH, and Every Common Fix (2026)

A lot of "how to install Claude Code" guides stop too early. They show one install command, maybe one login step, and then move on. Real beginners hit the actual problems **after** that point:

- Claude Code installs, but the command is not found
- Claude Code opens, but Git is missing
- Git exists, but the repo is not initialized
- The shell cannot see the API key or auth variables
- Homebrew works on one Mac but not another
- Apple Silicon and Intel behave slightly differently
- zsh, bash, and GUI terminal apps load different profiles

This guide is written for new users who want the full macOS setup path, not the marketing version.

## What Claude Code Needs on macOS

Before you even think about the install command, make sure you understand the dependency chain.

| Component | Why It Matters |
|-----------|----------------|
| Terminal app | Claude Code runs in the terminal |
| Node.js / npm | Claude Code is distributed as a CLI package |
| Git | Many agent workflows assume a Git repo and Git is part of normal coding workflow |
| Shell config | Your PATH and environment variables live here |
| Network access | Login, package install, and model access all require outbound access |
| API or account auth | Claude Code cannot do much if authentication is missing |

If one of these is missing, Claude Code may be installed correctly and still feel broken.

## macOS Versions, Intel vs Apple Silicon, and Terminal Choice

This guide assumes:

- macOS 12+
- Terminal.app or iTerm2
- zsh as the default shell (which is standard on modern macOS)

### Apple Silicon vs Intel

Most steps are identical, but package paths can differ:

- **Apple Silicon (M1 / M2 / M3 / M4)** Homebrew often lives at `/opt/homebrew`
- **Intel Mac** Homebrew often lives at `/usr/local`

That difference matters for PATH problems.

### Check your architecture

```bash
uname -m
```

Expected:

- `arm64` → Apple Silicon
- `x86_64` → Intel

## Step 1: Confirm Your Shell and Profile File

On modern macOS, your shell is usually zsh.

```bash
echo $SHELL
```

Common outputs:

- `/bin/zsh`
- `/bin/bash`

If you see zsh, the most important shell files are usually:

- `~/.zshrc`
- `~/.zprofile`

If you see bash, check:

- `~/.bashrc`
- `~/.bash_profile`

For most Claude Code setup work on macOS, **`~/.zshrc`** is the main file to update.

## Step 2: Install Homebrew If You Do Not Already Have It

Homebrew is the cleanest way to install Git, Node.js, and other developer tools on macOS.

Check whether it exists:

```bash
brew --version
```

If you get `command not found`, install Homebrew:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

After installation, Homebrew may ask you to add it to your shell profile.

For Apple Silicon, that usually looks like:

```bash
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"
```

For Intel:

```bash
echo 'eval "$(/usr/local/bin/brew shellenv)"' >> ~/.zprofile
eval "$(/usr/local/bin/brew shellenv)"
```

Verify:

```bash
which brew
brew --version
```

## Step 3: Install Git

A surprising number of new users install Claude Code first and only discover later that Git is missing.

Check Git:

```bash
git --version
```

If Git is missing, install it:

```bash
brew install git
```

Verify again:

```bash
which git
git --version
```

### Why Git matters for Claude Code

Even if Claude Code can launch without Git, you will quickly run into problems if you are not in a normal repository workflow:

- change review is harder
- diffs are harder
- rollback is harder
- some tools assume a Git repo

If you are starting a new folder, initialize it:

```bash
cd ~/Projects/my-app
git init
```

Optional but recommended:

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

## Step 4: Install Node.js and npm

Claude Code depends on a working Node.js/npm environment.

Check what you already have:

```bash
node --version
npm --version
```

If either command fails, install Node.js.

### Recommended option: Homebrew

```bash
brew install node
```

Verify:

```bash
node --version
npm --version
which node
which npm
```

### Better option for advanced users: nvm

If you switch Node versions often, use nvm instead.

Install nvm:

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.2/install.sh | bash
```

Then load it:

```bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
```

Install a stable Node version:

```bash
nvm install --lts
nvm use --lts
```

Verify:

```bash
node --version
npm --version
```

### Homebrew vs nvm: which should beginners choose?

- Choose **Homebrew** if you want the simplest path
- Choose **nvm** if you already know you will manage multiple Node versions

If you are brand new, Homebrew is usually less confusing.

## Step 5: Install Claude Code

Once Git and Node/npm are ready, install Claude Code.

First check whether it already exists:

```bash
which claude
claude --version
```

If not installed, use npm global install:

```bash
npm install -g @anthropic-ai/claude-code
```

Then verify:

```bash
which claude
claude --version
```

If successful, you should see a version such as:

```bash
2.1.76 (Claude Code)
```

## Step 6: Fix PATH Problems If `claude` Is Still Not Found

This is one of the most common macOS issues.

You run:

```bash
npm install -g @anthropic-ai/claude-code
```

It appears to succeed. Then:

```bash
claude --version
```

And you get:

```bash
zsh: command not found: claude
```

That usually means your npm global bin directory is not in PATH.

### Find the npm global bin directory

```bash
npm config get prefix
```

A common result is something like:

- `/opt/homebrew`
- `/usr/local`
- `/Users/you/.npm-global`

Then check the bin folder inside it.

Examples:

```bash
ls "$(npm config get prefix)/bin"
```

If `claude` is there, add it to PATH.

### Example PATH fix

```bash
echo 'export PATH="$(npm config get prefix)/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

Then verify again:

```bash
which claude
claude --version
```

### Homebrew path note

If npm globals are managed under Homebrew, also make sure Homebrew itself is loaded in your profile.

## Step 7: Authenticate and Set Environment Variables

Depending on your workflow, Claude Code may require account login and/or environment variables.

### First check whether the CLI offers login

```bash
claude --help
```

Look for auth, login, config, or provider-related commands.

### Common environment variable scenarios

You may need one or more of these depending on your setup:

- Anthropic key
- OpenAI-compatible base URL
- proxy variables
- custom provider settings

Examples:

```bash
export ANTHROPIC_API_KEY="your_key_here"
export OPENAI_BASE_URL="https://crazyrouter.com/v1"
export OPENAI_API_KEY="your_crazyrouter_key"
```

Persist them in `~/.zshrc`:

```bash
echo 'export ANTHROPIC_API_KEY="your_key_here"' >> ~/.zshrc
echo 'export OPENAI_BASE_URL="https://crazyrouter.com/v1"' >> ~/.zshrc
echo 'export OPENAI_API_KEY="your_crazyrouter_key"' >> ~/.zshrc
source ~/.zshrc
```

### Important caution

Do not put production secrets into screenshots, shared shell history dumps, or public Git repos.

## Step 8: Verify the Whole Environment, Not Just the Install

A proper Claude Code setup check should look like this:

```bash
which brew || true
brew --version || true

git --version || true
node --version || true
npm --version || true
claude --version || true

echo $SHELL
pwd
git status || true
```

You want to confirm:

- package manager works
- Git exists
- Node exists
- npm exists
- Claude Code exists
- shell profile loads
- current folder is a usable project

## Step 9: First Real Test in a Safe Folder

Create a test project:

```bash
mkdir -p ~/Projects/claude-code-test
cd ~/Projects/claude-code-test
git init
printf "# test\n" > README.md
```

Then try:

```bash
claude --help
```

And if supported by your version, a simple non-destructive prompt.

Start with something harmless:

- explain this folder
- suggest a README improvement
- generate a `.gitignore`

Do not start with `--dangerously-skip-permissions` style workflows until the basic environment is stable.

## Common macOS Problems and Fixes

## 1. `command not found: claude`

Cause:
- npm global bin not in PATH
- install failed silently
- shell profile not reloaded

Fix:

```bash
npm install -g @anthropic-ai/claude-code
export PATH="$(npm config get prefix)/bin:$PATH"
source ~/.zshrc
which claude
```

## 2. `git: command not found`

Cause:
- Git not installed
- Xcode command line tools not configured

Fix:

```bash
brew install git
```

or if macOS prompts for developer tools, allow that install and re-check.

## 3. `node: command not found` or wrong Node version

Cause:
- Node not installed
- nvm not loaded in current shell
- old system Node shadowing the right one

Fix:

```bash
which node
node --version
```

If using nvm, make sure the `nvm.sh` load lines are in your shell config.

## 4. Homebrew works in one terminal but not another

Cause:
- Terminal app loads different startup files
- Homebrew shellenv added to the wrong profile file

Fix:
- Add Homebrew init to `~/.zprofile`
- Add PATH exports to `~/.zshrc`
- restart the terminal fully

## 5. Environment variables exist in one tab but disappear in another

Cause:
- variables were exported only for the current session
- not persisted to shell config

Fix:
- write them into `~/.zshrc`
- run `source ~/.zshrc`
- open a new terminal tab and test again

## 6. Corporate network or proxy blocks install/login

You may need proxy environment variables:

```bash
export HTTPS_PROXY="http://proxy.example.com:8080"
export HTTP_PROXY="http://proxy.example.com:8080"
```

If npm fails, also check:

```bash
npm config get proxy
npm config get https-proxy
```

## 7. Permission denied during global npm install

Avoid random `sudo npm install -g` unless you understand the ownership issue.

Better fixes:
- use Homebrew-managed Node
- use nvm
- use a user-owned npm prefix

If permissions are already broken, inspect ownership before changing anything.

## Recommended macOS Setup for Beginners

If you want the least confusing setup, use this stack:

- Terminal.app or iTerm2
- Homebrew
- Git via Homebrew
- Node via Homebrew
- Claude Code via npm global install
- zsh with variables saved in `~/.zshrc`

That path is not the fanciest, but it is the easiest to debug.

## FAQ

### Do I need Git before installing Claude Code on macOS?

Strictly speaking, the package install may succeed without Git. In practice, most real Claude Code workflows become painful without Git, so yes, install Git first.

### Does Claude Code work on Apple Silicon Macs?

Yes. The main difference is package paths, especially for Homebrew, which usually lives under `/opt/homebrew` on Apple Silicon.

### Should I use Homebrew or nvm for Claude Code dependencies?

For beginners, Homebrew is simpler. For developers who juggle multiple Node versions, nvm is more flexible.

### Why is Claude Code installed but still not available in zsh?

Usually because the npm global bin directory is not in PATH, or because your shell profile was not reloaded after installation.

### Can I use Claude Code with a gateway instead of direct Anthropic setup?

Yes, if your workflow supports OpenAI-compatible or other configurable endpoints. That is especially useful when you want one key for multiple model families and simpler billing.

## Final Take

The real Claude Code setup on macOS is not just one install command. It is a chain:

1. terminal
2. Homebrew
3. Git
4. Node/npm
5. Claude Code
6. PATH
7. environment variables
8. real project verification

If you fix those in order, almost every beginner setup issue becomes much easier to diagnose.
