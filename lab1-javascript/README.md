# Lab 1: JavaScript Foundations
**67-336 Data Visualization | Fall 2026**

It is **crucial** that you read the following instructions before working on anything in this project. Throughout this lab, make sure to commit regularly and push your changes to GitHub.

***Have fun learning Javascript!***

---

## Before You Start

Make sure you have completed Lab 0. You should already have:
- Git installed and configured with your CMU email
- Node.js v22.17.0 installed
- A GitHub account

If any of these are missing, go back to Lab 0 before continuing.

---

## Terminal Quick Reference

| Command | What it does |
|---|---|
| `ls` | List files and folders in your current location |
| `cd folder` | Move into a folder |
| `cd ..` | Go back up one level |
| `pwd` | Show your current location |
| `git clone <url>` | Download a remote repository to your machine |
| `git remote -v` | See which remote your repo is connected to |

---

## Step 1: Check for Node.js and VSCode

Make sure Node is installed by going to your terminal and typing:

```bash
node -v
```

You should see `v22.17.0`. You do NOT need to open a new terminal window for this.

If Node is not installed, go back to Lab 0 and complete Part 4 before continuing.

Also make sure [VSCode](https://code.visualstudio.com/) is installed. We will use it to edit files throughout this lab.

---

## Step 2: Clone the Starter Repository

Create a folder on your computer for 67-336 labs. Open your terminal inside that folder, then clone the starter repo:

```bash
git clone https://github.com/CMU-67336-Data-Visualization/Lab01_JavaScriptFoundations.git
```

After cloning, use `ls` to confirm the folder was created:

```bash
ls
```

You should see `Lab01_JavaScriptFoundations` listed. Now move into it:

```bash
cd Lab01_JavaScriptFoundations
```

Run `ls` again to see the starter files:

```bash
ls
```

You should see `index.js`, `index.html`, and `package.json`.

---

## Step 3: Create Your Own Private GitHub Repo

1. Go to [github.com](https://github.com) and sign in.
2. Click the **+** icon in the top-right corner and choose "New repository".
3. Name it exactly:
```
67336_Lab1
```
4. Set visibility to **Private**.
5. Leave everything else unchecked — do not add a README or .gitignore.
6. Click "Create repository".

Then add your instructors as collaborators so they can grade your work:

7. Go to your new repo → **Settings** → **Collaborators** → **Add people**
8. Add each of the following one at a time:
   - `shihongh`
   - `ygonz174`
   - `lillian-zhao`

> WARNING: You must do this so we can grade your lab.

---

## Step 4: Connect the Starter Repo to Your New GitHub Repo

### 1. Check the Current Remote

```bash
git remote -v
```

You should see the class repo URL. That is expected.

### 2. Remove the Existing Remote

```bash
git remote remove origin
```

Verify it was removed — this command should return no output:

```bash
git remote -v
```

### 3. Add Your New Remote

```bash
git remote add origin https://github.com/YOUR-USERNAME/67336_Lab1.git
```

Replace `YOUR-USERNAME` with your GitHub username. Verify:

```bash
git remote -v
```

You should now see your personal repo URL.

### 4. Initialize package.json

```bash
npm init -y
```

Open `package.json` and replace the `"scripts"` section with:

```json
"scripts": {
  "start": "node index.js",
  "test": "echo \"Error: no test specified\" && exit 1"
}
```

### 5. Push to Your Repository

```bash
git add .
git commit -m "Initial commit from starter repo and created package.json"
git push --set-upstream origin main
```

Go to GitHub and confirm the files are populated in your repository. If not, stop and ask a TA for help before continuing.

---

## Step 5: Use a Development Branch

For best coding practices, you should **not** be working on the main branch. To keep your `main` branch clean:

1. Create and switch to a new branch:

```bash
git checkout -b lab-dev
```

2. As you make changes, commit and push regularly:

```bash
git add .
git commit -m "Complete Challenge 1"
git push -u origin lab-dev
```

> Tip: Commit after completing each challenge section, not just at the end. This builds good habits and gives you checkpoints to return to if something breaks.

3. When you are finished and everything works, merge back into main:

```bash
git checkout main
git merge lab-dev
git push
```

---

## Step 6: Run the Starter Code

Install dependencies and run the starter file:

```bash
npm install
npm start
```

You should see output printed to your terminal. Read through it. This gives you a preview of the JavaScript concepts you will be working with.

---

## Step 7: Complete the index.js and index.html Files

Open `index.js` and `index.html` in VSCode and work through each challenge in order. Each challenge has:
- An **example** showing the concept
- A **Your Turn** section where you write your own code

**Tips:**
- Run `npm start` after each challenge to check your output in the terminal
- For Challenges 3, 4, and 5, open `index.html` in Chrome using Live Server (right-click → Open with Live Server)
- Open the browser console with `Cmd+Option+J` (Mac) or `Ctrl+Shift+J` (Windows) to see output and errors
- Use [W3Schools](https://www.w3schools.com/js/) for reference

> WARNING: **Do NOT use AI to complete this lab.** The goal is to build your own understanding of JavaScript fundamentals. You will need these skills for every lab and project in this course.

---

## Step 8: Deployment on Vercel

We will use [Vercel](https://vercel.com) to deploy your JavaScript project so it is accessible at a live public URL.

### 1. Create a Vercel Account

Go to [https://vercel.com](https://vercel.com) and sign up with your **GitHub account**.

### 2. Import Your Project from GitHub

1. On the Vercel dashboard, click **"Add New Project"**
2. Select your `67336_Lab1` repo
3. For **Framework Preset**, choose **Other** (this is a basic HTML + JS project)
4. Set the following:
   - **Root Directory:** `./` (or leave blank)
   - **Build Command:** leave blank
   - **Output Directory:** `./` or leave blank
5. Click **Deploy**

### 3. Get Your Live URL

After deployment finishes, you will get a live URL like:

```
https://67336-lab1-yourname.vercel.app
```

Open it and confirm your `index.html` loads correctly in the browser.

---

## Step 9: Submitting on Canvas

Submit the following on Canvas:

- Your **GitHub repo link** (e.g. `https://github.com/YOUR-USERNAME/67336_Lab1`)
- Your **Vercel live site link** (e.g. `https://67336-lab1-yourname.vercel.app`)

> Make sure you have added `shihongh`,`ygonz174`, and `lillian-zhao` as collaborators before submitting.

---

## Quick Reference: Git Commands Used This Lab

| Command | What it does |
|---|---|
| `git clone <url>` | Download a repo to your machine |
| `git remote -v` | See which remote you are connected to |
| `git remote remove origin` | Disconnect from the current remote |
| `git remote add origin <url>` | Connect to a new remote |
| `git checkout -b lab-dev` | Create and switch to a new branch |
| `git add .` | Stage all changes |
| `git commit -m "message"` | Save staged changes with a message |
| `git push -u origin lab-dev` | Push branch to GitHub for the first time |
| `git checkout main` | Switch back to main |
| `git merge lab-dev` | Merge lab-dev into main |
| `git push` | Push current branch to GitHub |

---

That's it! You've cloned a project, set up Git, written JavaScript foundations, and deployed a live site.