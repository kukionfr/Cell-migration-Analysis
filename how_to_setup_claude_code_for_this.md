# How to Set Up Claude Code on Windows and Use It for This Project

This guide walks you through installing Claude Code on Windows and using it to clone this repository, review the codebase, write documentation, and submit a pull request — exactly as was done to create this project's README.

---

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Install Git for Windows](#step-1-install-git-for-windows)
3. [Install Claude Code](#step-2-install-claude-code)
4. [Authenticate Claude Code](#step-3-authenticate-claude-code)
5. [Install GitHub CLI](#step-4-install-github-cli)
6. [Authenticate GitHub CLI](#step-5-authenticate-github-cli)
7. [Reproduce What We Did](#step-6-reproduce-what-we-did)

---

## Prerequisites

- Windows 10 version 1809 or later (or Windows Server 2019+)
- 4 GB+ RAM
- An internet connection
- A GitHub account
- A Claude subscription (Pro or Max plan) OR an Anthropic API key

---

## Step 1: Install Git for Windows

Claude Code on native Windows requires Git Bash.

1. Download Git for Windows from https://git-scm.com/downloads/win
2. Run the installer and follow the prompts (default settings are fine)
3. Verify installation by opening **PowerShell** and running:

```powershell
git --version
```

You should see something like `git version 2.x.x.windows.x`.

---

## Step 2: Install Claude Code

### Option A: Native Windows Install (Recommended)

Open **PowerShell** and run:

```powershell
irm https://claude.ai/install.ps1 | iex
```

Or if using **Command Prompt (CMD)**:

```batch
curl -fsSL https://claude.ai/install.cmd -o install.cmd && install.cmd && del install.cmd
```

### Option B: Using WinGet

```powershell
winget install Anthropic.ClaudeCode
```

### Option C: Using WSL (Windows Subsystem for Linux)

If you prefer a Linux environment inside Windows:

1. Install WSL by opening PowerShell as Administrator:

```powershell
wsl --install
```

2. Restart your computer when prompted
3. Open your WSL terminal (Ubuntu by default) and install Claude Code:

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

> **Note:** WSL 2 is recommended over WSL 1 for full sandboxing support.

### Verify Installation

After installing, open a new terminal and run:

```bash
claude doctor
```

This checks that everything is configured correctly. If it reports issues, follow its suggestions to fix them.

> **Tip:** If Claude Code can't find Git Bash, set the path explicitly in PowerShell:
> ```powershell
> $env:CLAUDE_CODE_GIT_BASH_PATH="C:\Program Files\Git\bin\bash.exe"
> ```

---

## Step 3: Authenticate Claude Code

You need an active Claude subscription or API key to use Claude Code.

### Option A: Claude Pro or Max Plan (Recommended)

1. Subscribe at https://claude.ai/pricing
2. Launch Claude Code:

```bash
claude
```

3. When prompted, select **"Claude.ai account"**
4. A browser window opens — log in with your Claude.ai account
5. Authorize Claude Code when prompted
6. You're ready to go

### Option B: Anthropic API Key

1. Get an API key from https://console.anthropic.com
2. Set it as an environment variable:

In **PowerShell**:
```powershell
$env:ANTHROPIC_API_KEY="sk-ant-your-key-here"
```

In **Git Bash** or **WSL**:
```bash
export ANTHROPIC_API_KEY="sk-ant-your-key-here"
```

3. Launch Claude Code:

```bash
claude
```

---

## Step 4: Install GitHub CLI

The GitHub CLI (`gh`) is used for cloning repos, forking, and creating pull requests.

### Using WinGet (Recommended)

```powershell
winget install GitHub.cli
```

### Using Installer

1. Download from https://cli.github.com
2. Run the installer
3. Verify:

```bash
gh --version
```

### In WSL

```bash
sudo apt update && sudo apt install gh
```

---

## Step 5: Authenticate GitHub CLI

1. Run:

```bash
gh auth login
```

2. Follow the prompts:
   - Select **GitHub.com**
   - Select **HTTPS** (recommended) or **SSH**
   - Authenticate via **browser** (easiest) — it will open a browser window for you to log in and authorize

3. Verify:

```bash
gh auth status
```

You should see your GitHub account listed as the active account.

---

## Step 6: Reproduce What We Did

Now you have Claude Code and GitHub CLI set up. Here's exactly how to replicate the workflow from this session.

### 6.1 Clone the Repository

Open your terminal (Git Bash, PowerShell, or WSL) and navigate to where you want the project:

```bash
gh repo clone DeepBioVision/Cell-migration-Analysis
cd Cell-migration-Analysis
```

### 6.2 Launch Claude Code

From inside the project directory, start Claude Code:

```bash
claude
```

### 6.3 Ask Claude Code to Explore and Write the README

Once Claude Code is running, type the following prompt:

```
use explore agent to review the codebase and write a comprehensive README
```

Claude Code will:
1. Use its **Explore agent** to scan all files in the repository (MATLAB scripts, data files, etc.)
2. Analyze the project structure, dependencies, functions, and data flow
3. Write a detailed `README.md` with sections for overview, installation, usage, API reference, etc.

### 6.4 Review the Changes

Before committing, you can ask Claude Code to show you what changed:

```
show me the README you wrote
```

Or you can review the file yourself in your editor.

### 6.5 Commit and Push

Ask Claude Code to commit and push:

```
commit and push this
```

Claude Code will:
1. Stage the modified `README.md`
2. Create a commit with a descriptive message
3. Push to the remote repository

> **Note:** If you don't have write access to the original repository, Claude Code will fork it under your account and push there instead.

### 6.6 Create a Pull Request

Ask Claude Code to create a PR:

```
create a PR to the original repo
```

Claude Code will use `gh pr create` to open a pull request from your fork to the upstream `DeepBioVision/Cell-migration-Analysis` repository. It will generate a PR title and description summarizing the changes.

---

## Summary of Commands

Here is the complete sequence of commands and prompts:

```bash
# Terminal commands
gh repo clone DeepBioVision/Cell-migration-Analysis
cd Cell-migration-Analysis
claude
```

```
# Inside Claude Code (type these as prompts)
use explore agent to review codebase and write a readme
commit and push this
create a PR to the original repo
```

That's it — three prompts inside Claude Code to go from an empty README to a comprehensive one with a pull request submitted.

---

## Troubleshooting

| Problem | Solution |
|---------|----------|
| `claude` command not found | Close and reopen your terminal, or run the install script again |
| Authentication fails | Run `claude doctor` to diagnose, then re-run `claude` to re-authenticate |
| `gh` command not found | Close and reopen terminal after installing GitHub CLI |
| Push permission denied | Claude Code will automatically fork the repo if you lack write access |
| Git Bash not found | Set `CLAUDE_CODE_GIT_BASH_PATH` environment variable to your bash.exe path |
| WSL not installing | Ensure virtualization is enabled in BIOS/UEFI settings |

---

## Additional Resources

- Claude Code documentation: https://docs.anthropic.com/en/docs/claude-code
- GitHub CLI manual: https://cli.github.com/manual
- Git for Windows: https://gitforwindows.org
