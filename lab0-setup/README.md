# Lab 0: Git, GitHub & Node.js Setup
**67-336 Data Visualization | Fall 2026**

---

## Overview

In this lab you will set up all the tools you need for the semester: Git, GitHub, and Node.js. By the end you will have a working development environment and hands-on experience with the full version control workflow.

---

## Before You Start: Getting Comfortable with the Terminal

The terminal is a text-based interface that lets you control your computer by typing commands. Many students have not used it before, and that is completely fine! Here is a quick primer on the commands you will use today.

| Command | What it does |
|---|---|
| `ls` | List all files and folders in your current location |
| `cd folder` | Change into a folder (e.g. `cd Documents`) |
| `cd ..` | Go back up one folder level |
| `mkdir name` | Create a new folder |
| `touch file` | Create a new empty file |
| `rm file` | Delete a file (permanent, be careful!) |
| `pwd` | Print your current location (full path) |

**Mac/Linux:** Open Spotlight (Cmd+Space), type Terminal, and press Enter. Right-click any folder in Finder and choose "Open Terminal Here" to start already inside that folder.

**Windows:** Press the Windows key, search PowerShell, and press Enter. Right-click any folder in File Explorer and choose "Open in Terminal" (Windows 11) or "Open PowerShell window here" (Windows 10).

---

## Think of Git Like a Shortbread Cookie Recipe

Imagine you are baking shortbread cookies.

- **Working Directory = Your kitchen counter.** You are actively mixing ingredients here. Nothing is saved yet.
- **Staging Area = The baking tray.** You choose which cookies (files) to put on the tray before baking (`git add`).
- **Repository = The finished tin of cookies.** Once baked (committed), the recipe is stored permanently with a label.

**The recipe scenario we will follow:**
1. You write a shortbread recipe. The yield says 12 cookies.
2. You bake it and discover it actually makes 3. You fix the file and commit.
3. You taste them and realize 3 was also wrong, it actually makes 24. You fix it and commit again.

---

## Learning Objectives

By the end of this lab, you will be able to:
- Install and configure Git on your machine
- Create and manage a GitHub account linked to your CMU email
- Navigate your file system using terminal commands
- Execute the core Git workflow: add, commit, push
- Install Node.js using a version manager

---

## Part 1: Installing & Configuring Git

Git is a version control system, it tracks every change you make to your files so you can always go back in time. Think of it as an infinite undo button for your entire project.

---

### Step 1: Check if Git is already installed

Run this command in your terminal:

```bash
git --version
```

If you see something like `git version 2.41.0`, Git is already installed. Skip to Step 3.

If you see an error or "command not found", continue to Step 2.

---

### Step 2: Install Git

**Mac/Linux:** Download the Mac installer from https://git-scm.com/downloads and follow the on-screen steps. When finished, rerun `git --version`.

> Tip: On newer Macs, you may be prompted to install Xcode Command Line Tools, go ahead and accept. It includes Git.

**Windows:** Download the Windows installer from https://git-scm.com/downloads and accept all the defaults during setup. When finished, open a new PowerShell window and run `git --version`.

> Tip: During installation, when asked about the default branch name, choose "main".

---

### Step 3: Configure your Git identity

Tell Git who you are. Run these two commands, replacing the placeholder text with your own info:

```bash
git config --global user.name "Your Full Name"
git config --global user.email "yourID@andrew.cmu.edu"
```

> Note: The quotation marks are required. Do not remove them.

Confirm your settings were saved:

```bash
git config --list
```

You should see your name and CMU email listed under `user.name` and `user.email`.

---

## Part 2: Setting Up GitHub

GitHub is where your Git repositories live in the cloud. It lets you back up your work, collaborate with teammates, and share your projects with instructors and TAs.

---

### Step 4: Create or verify your GitHub account

Find your situation below and follow only those steps:

**A — You have a GitHub account with your CMU email AND an active Education Plan.**
You are all set. Skip to Step 5.

**B — You have a GitHub account with your CMU email but NO Education Plan.**
Go to https://github.com/edu, click "Join GitHub Education", and sign in with your existing account. Then skip to Step 5.

**C — You do not have a GitHub account yet.**
Go to https://github.com/, click "Sign Up", and use your CMU email to create an account. Then go to https://github.com/edu and activate your Education Plan. Then continue to Step 5.

---

### Step 5: Create a new private repository on GitHub

1. Go to github.com and sign in.
2. Click the **+** icon in the top-right corner and choose "New repository".
3. Name your repository `lab0-setup`.
4. Set visibility to **Private**.
5. Do NOT check any boxes for README, .gitignore, or license — leave the repo empty.
6. Click "Create repository" and leave the page open — you will need the URL in Step 11.

> Tip: Keep your repositories private unless instructed otherwise. This protects your work from being copied by other students.

---

## Part 3: Git in Practice, The Cookie Recipe

Now let's put it all together. You will practice the full Git workflow using a simple text file before applying these same skills to real project code.

---

### Step 6: Create a local repository

In your terminal, create a new folder and initialize a Git repository inside it:

```bash
mkdir cookie_recipes
cd cookie_recipes
git init
```

After running `mkdir`, use `ls` to confirm the folder was created before `cd`-ing into it:

```bash
ls
```

You should see `cookie_recipes` listed. Then `cd` into it and run `git init`.

> Tip: `git init` creates a hidden `.git` folder inside your project that stores your entire version history. Never delete it!

---

### Step 7: Create a file and make your first commit

Create a new file called `shortbread.txt`:

```bash
touch shortbread.txt
```

Open the file in any text editor and add this content:

```bash
Shortbread Cookies
Yield: 12 cookies
Ingredients:

1 cup butter
1/2 cup powdered sugar
2 cups flour
```

Save the file, then check Git's view of your project:

```bash
git status
```

The file appears as `untracked`, Git sees it but is not watching it yet.

Add it to the staging area (place it on the baking tray):

```bash
git add shortbread.txt
```

Commit it to the repository (bake it into the tin):

```bash
git commit -m "Add initial shortbread recipe, yield 12 cookies"
```

> Tip: Write commit messages that describe what changed and why. Your future self will thank you.

---

### Step 8: Make a change and view the difference

You just discovered the recipe actually yields 3 cookies, not 12.

Open `shortbread.txt` and change `Yield: 12 cookies` to `Yield: 3 cookies`.

Save the file, then see exactly what changed:

```bash
git diff shortbread.txt
```

Lines starting with `-` were removed. Lines starting with `+` were added.

Stage and commit the fix:

```bash
git add shortbread.txt
git commit -m "Fix yield: recipe makes 3 cookies, not 12"
```

---

### Step 9: Make another change and commit

You taste the cookies and realize 3 was also wrong, the actual yield is 24.

Open `shortbread.txt` and change `Yield: 3 cookies` to `Yield: 24 cookies`.

Save the file, then see exactly what changed:

```bash
git diff shortbread.txt
```

Stage and commit the correction:

```bash
git add shortbread.txt
git commit -m "Correct yield: recipe makes 24 cookies"
```

View the full history:

```bash
git log --oneline
```

You should now see three commits, one for each change you made. This is version control in action since every saved state of your recipe is recorded and recoverable.

---

### Step 10: Push to GitHub

Connect your local repo to the private GitHub repo you created in Step 5. Copy the HTTPS URL from your GitHub repository page, then run:

```bash
git remote add origin https://github.com/YOUR_USERNAME/lab0-setup.git
git branch -M main
git push -u origin main
```

After pushing, use `ls` to confirm your files are still in place locally:

```bash
ls
```

Refresh your GitHub repository page, your files and all three commits should be visible online.

> Note: You can also use [GitHub Desktop](https://desktop.github.com/) as a graphical alternative to the command line for cloning and pushing, if you prefer a visual interface.

**STOP:** Show a TA your GitHub repository with all three commits visible in the commit history before moving on.

---

## Part 4: Installing Node.js

Node.js lets you run JavaScript outside the browser. We use it throughout this course to power Observable notebooks and build interactive data visualizations.

---

### Step 11: Install Node.js using a version manager

We use a version manager so you can easily switch Node versions if needed later.

**Mac/Linux:**

Step 1 — Install nvm:
```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
```

Step 2 — Activate nvm without restarting your terminal:
```bash
. "$HOME/.nvm/nvm.sh"
```

Step 3 — Install Node 22:
```bash
nvm install 22
```

**Windows:**

Step 1 — Install Chocolatey (run PowerShell as Administrator):
```bash
powershell -c "irm https://community.chocolatey.org/install.ps1|iex"
```

Step 2 — Install Node 22:
```bash
choco install nodejs-lts --version="22"
```

---

### Step 12: Verify the installation

You do NOT need to open a new terminal window, run these in the same terminal:

```bash
node -v
```
Expected: `v22.17.0`

```bash
npm -v
```
Expected: `10.9.2`

> ⚠️ Warning: If the versions do not match, close your terminal, reopen it, and run the commands again before troubleshooting further.

**STOP:** Show a TA your terminal with `node -v` returning `v22.17.0` and `npm -v` returning `10.9.2` before moving on.

---

## Submission

Submit the following on Canvas:

- Your **GitHub repo link** (e.g. `https://github.com/YOUR-USERNAME/lab0-setup`)

> ⚠️ Make sure your repo is **private**, has at least **3 commits** visible, and add `shihongh`, `ygonz174`, and `lillian-zhao` as collaborators so we can grade your lab.

---

## Quick Reference: Essential Git Commands

| Command | What it does |
|---|---|
| `git init` | Create a new local repository |
| `git status` | See what files have changed |
| `git add <file>` | Stage a specific file for commit |
| `git add .` | Stage ALL changed files at once |
| `git commit -m "message"` | Save staged files with a descriptive message |
| `git log --oneline` | View a compact commit history |
| `git diff <file>` | See line-by-line changes not yet staged |
| `git push origin main` | Upload commits to GitHub |
| `git pull origin main` | Download latest commits from GitHub |
| `git clone <url>` | Download a remote repository to your machine |
| `git remote -v` | See which remote repository you are connected to |
| `git remote add origin <url>` | Connect your local repo to a GitHub repository |
| `git remote remove origin` | Disconnect from the current remote |
| `git branch <name>` | Create a new branch |
| `git checkout <branch>` | Switch to a branch |
| `git merge <branch>` | Merge a branch into the current one |

---

That's it! You have installed Git, set up GitHub, practiced version control with a cookie recipe, and installed Node.js.