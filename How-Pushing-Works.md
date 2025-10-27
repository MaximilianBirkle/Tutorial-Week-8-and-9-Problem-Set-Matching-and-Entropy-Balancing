# Quick Git Push Guide

## For Mac Users (Terminal)

### Step 1: Navigate to Project Folder (Example folder from my directory)
```bash
cd "/Users/maximilianbirkle/Assignment 4/Tutorial-Week-6-Difference-in-Differences-and-Diagnostics"
```

Or use shortcut (if configured):
```bash
assignment4
```

### Step 2: Pull Latest Changes
```bash
git pull
```

### Step 3: Stage, Commit, and Push
```bash
# Stage all changes
git add .

# Commit with a message
git commit -m "Brief description of what you changed"

# Push to GitHub
git push
```

### Complete Workflow
```bash
cd "/Users/maximilianbirkle/Assignment 4/Tutorial-Week-6-Difference-in-Differences-and-Diagnostics"
git pull
git add .
git commit -m "Your message here"
git push
```

---

## For Windows Users (Git Bash or Command Prompt)

### Step 1: Navigate to Project Folder

**Using Git Bash:**
```bash
cd "/c/Users/YourUsername/Documents/Tutorial-Week-6-Difference-in-Differences-and-Diagnostics"
```

**Using Command Prompt:**
```cmd
cd "C:\Users\YourUsername\Documents\Tutorial-Week-6-Difference-in-Differences-and-Diagnostics"
```

**Or navigate using File Explorer:**
1. Open File Explorer
2. Navigate to your project folder
3. Right-click in the folder → "Git Bash Here" or "Open in Terminal"

### Step 2: Pull Latest Changes
```bash
git pull
```

### Step 3: Stage, Commit, and Push
```bash
# Stage all changes
git add .

# Commit with a message
git commit -m "Brief description of what you changed"

# Push to GitHub
git push
```

### Complete Workflow (Git Bash)
```bash
cd "/c/Users/YourUsername/Documents/Tutorial-Week-6-Difference-in-Differences-and-Diagnostics"
git pull
git add .
git commit -m "Your message here"
git push
```

### Complete Workflow (Command Prompt)
```cmd
cd "C:\Users\YourUsername\Documents\Tutorial-Week-6-Difference-in-Differences-and-Diagnostics"
git pull
git add .
git commit -m "Your message here"
git push
```

---

## Using RStudio (Easiest - Works on Both Mac & Windows!)

### Recommended Method for All Users

1. **Open RStudio**
2. **Open Project:** File → Open Project → Select `.Rproj` file
3. **Pull first:** Click blue ⬇️ "Pull" button (top-right Git pane)
4. **Make your edits** to the `.Rmd` file
5. **Save and Knit** to test (Ctrl+Shift+K or Cmd+Shift+K)
6. **In Git pane:** Check boxes next to modified files
7. **Commit:** Click "Commit" button
8. **Write message** describing your changes
9. **Commit & Push:** Click "Commit", then click green ⬆️ "Push" button

---

## Important Rules

### ✅ Always Do This:
- **Pull before you start working** (`git pull`)
- Knit your `.Rmd` to make sure it works before pushing
- Write clear commit messages
- Push at the end of each work session
- Communicate with your team about what you're working on

### ❌ Never Do This:
- Don't push broken code (always knit first!)
- Don't work for hours without pulling
- Don't share your Personal Access Token
- Don't use vague commit messages like "update" or "fixes"

---

## Good Commit Messages

### ✅ Good Examples:
- `"Completed Q1 data setup and variable creation"`
- `"Added density plots for Q2"`
- `"Fixed error in parallel trends test"`
- `"Updated interpretation for Q5 placebo test"`

### ❌ Bad Examples:
- `"update"`
- `"changes"`
- `"asdf"`
- `"final version"` (it's never final!)

---

## Troubleshooting

### "Permission denied" or "Authentication failed"
- You need your Personal Access Token (not your GitHub password)
- Generate one at: GitHub.com → Settings → Developer settings → Personal access tokens

### "Your branch is behind 'origin/main'"
```bash
git pull
git push
```

### "Merge conflict"
1. Open the conflicting file (Git will tell you which one)
2. Look for `<<<<<<<`, `=======`, `>>>>>>>` markers
3. Decide what to keep, remove the markers
4. Save the file
```bash
git add .
git commit -m "Resolved merge conflict"
git push
```

### "Not a git repository"
- You're in the wrong folder
- Navigate to the project folder first (see Step 1)

### Vim opened and you're stuck
- Press `Esc`
- Type `:wq` and press `Enter`

---

## First Time Setup (All Users - Mac & Windows)

### Step 1: Install Required Software

1. **Git:** 
   - Mac: Download from https://git-scm.com/download/mac (or install via Xcode)
   - Windows: Download from https://git-scm.com/download/win
   
2. **R:** Download from https://cran.r-project.org/

3. **RStudio:** Download from https://posit.co/download/rstudio-desktop/

4. **LaTeX (for PDF knitting):**
   - Open R or RStudio and run:
   ```r
   install.packages('tinytex')
   tinytex::install_tinytex()
   ```
   - Wait 5-10 minutes for installation to complete
   - Restart RStudio after installation

### Step 2: Configure Git

Open Terminal (Mac) or Git Bash (Windows):

```bash
git config --global user.name "Your Full Name"
git config --global user.email "your.email@example.com"
```

**Important:** Use the same email as your GitHub account!

### Step 3: Get GitHub Personal Access Token

1. Go to https://github.com/settings/tokens
2. Click "Personal access tokens" → "Tokens (classic)"
3. Click "Generate new token" → "Generate new token (classic)"
4. Give it a name (e.g., "RStudio Laptop")
5. Set expiration (e.g., 90 days)
6. Check the box for **`repo`** (full control of repositories)
7. Click "Generate token" at the bottom
8. **Copy the token immediately!** (starts with `ghp_...`)
9. Save it somewhere safe (you'll need it soon)

### Step 4: Clone the Repository

**Option A: Using RStudio (Recommended)**

1. Open RStudio
2. File → New Project → Version Control → Git
3. **Repository URL:** 
   ```
   https://github.com/MaximilianBirkle/Tutorial-Week-6-Difference-in-Differences-and-Diagnostics.git
   ```
4. **Project directory name:** Will auto-fill
5. **Create project as subdirectory of:** Choose a location (e.g., Documents)
6. Click "Create Project"
7. When prompted for credentials:
   - Username: Your GitHub username
   - Password: **Paste your Personal Access Token** (not your GitHub password!)

**Option B: Using Terminal/Git Bash**

**Mac:**
```bash
cd ~/Documents
git clone https://github.com/MaximilianBirkle/Tutorial-Week-6-Difference-in-Differences-and-Diagnostics.git
cd Tutorial-Week-6-Difference-in-Differences-and-Diagnostics
```

**Windows (Git Bash):**
```bash
cd /c/Users/YourUsername/Documents
git clone https://github.com/MaximilianBirkle/Tutorial-Week-6-Difference-in-Differences-and-Diagnostics.git
cd Tutorial-Week-6-Difference-in-Differences-and-Diagnostics
```

When prompted:
- Username: Your GitHub username  
- Password: **Your Personal Access Token**

### Step 5: Install Required R Packages

Open the project in RStudio and run:

```r
# Install all required packages
install.packages(c(
  "tidyverse",    # Data manipulation and visualization
  "haven",        # Reading data files
  "knitr",        # Tables
  "kableExtra",   # Enhanced tables
  "broom",        # Tidy regression output
  "lmtest",       # Hypothesis testing
  "car",          # Linear hypothesis tests
  "gridExtra"     # Multiple plots
))
```

This may take 5-15 minutes depending on your internet connection.

### Step 6: Test Everything Works

1. Open `Tutorial Week 6.Rmd` in RStudio
2. Try knitting to PDF: Click "Knit" button (or Ctrl+Shift+K / Cmd+Shift+K)
3. If it successfully creates a PDF, you're all set! ✅

### Step 7: Configure Git Credentials in RStudio (Optional but Recommended)

This saves your token so you don't have to enter it every time:

```r
# Install credential helper packages
install.packages(c("usethis", "gitcreds"))

# Store your token
gitcreds::gitcreds_set()
```

When prompted, paste your Personal Access Token.

Now RStudio will remember your credentials!

---

## Team Communication

Before pushing, message your team:
- "Starting work on Q3"
- "Finished Q5, pushing now"
- "About to push changes to Q7-Q9"

This prevents conflicts and keeps everyone in sync!

---

## Summary: The Three Essential Commands

```bash
git pull          # Get latest changes (do this FIRST!)
git add .         # Stage your changes
git commit -m "Your message"   # Save your changes
git push          # Upload to GitHub
```

**Remember:** Pull → Work → Save → Knit → Add → Commit → Push

---

Last updated: October 13, 2025
