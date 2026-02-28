# 📘 Git Practical Guide

This document explains Git practical tasks step-by-step in simple language.
It covers repository setup, feature workflow, squash, reset, rebase, cherry-pick, hooks, tagging, and PR strategy.

---

# ✅ PART 1 — Repository Setup

## 1️⃣ Create a New Repository

### Option A – From GitHub Template
1. Go to GitHub
2. Click **Create Repository**
3. Click **"Use this template"**
4. Create your repo

👉 Used when company provides a base project structure.

---

### Option B – Create Repository Locally

```bash
mkdir git-practical
cd git-practical
git init
```

### 🔹 What `git init` does:
- Creates a hidden `.git` folder
- Converts normal folder into Git repository

---

Create README file:

```bash
echo "# Git Practical" > README.md
git add .
git commit -m "Initial commit"
```

### 🔹 `git add .`
Moves files to staging area.

### 🔹 `git commit`
Saves snapshot in Git history.

---

## 2️⃣ Initialize Git Flow

```bash
git flow init
```

Press Enter for default values:

- main → Production branch
- develop → Development branch
- feature/ → For features
- release/ → For releases
- hotfix/ → For urgent fixes

Switch to develop:

```bash
git checkout develop
```

---

# ✅ PART 2 — Feature Development

## 3️⃣ Create Feature Branch

```bash
git checkout develop
git checkout -b feature/TP2-T1299_Project_Setup
```

👉 Why?
- Every task should have its own branch.
- Keeps develop branch clean.

---

## 4️⃣ Create Sub-Branch

```bash
git checkout -b feature/TP2-T1299_Subtask
```

👉 Used when feature is large and needs smaller tasks.

---

# 🔥 Perform Important Git Operations

---

# 🔹 A) Multiple Commits

```bash
echo "Line 1" >> file.txt
git add .
git commit -m "TP2-T1299 Added line 1"

echo "Line 2" >> file.txt
git add .
git commit -m "TP2-T1299 Added line 2"

echo "Line 3" >> file.txt
git add .
git commit -m "TP2-T1299 Added line 3"
```

👉 This creates 3 separate commits.

Used to simulate real development changes.

---

# 🔹 B) SQUASH (Combine Commits)

Used to clean commit history before creating PR.

```bash
git rebase -i HEAD~3
```

Change:

```
pick
pick
pick
```

To:

```
pick
squash
squash
```

Save file.

👉 Result:
3 commits become 1 clean commit.

✅ Use when:
- Cleaning commit history
- Following "1 commit per PR" rule

---

# 🔹 C) RESET (Undo Commit)

If commit NOT pushed:

```bash
git reset --soft HEAD~1
```

👉 Removes last commit  
👉 Keeps changes staged

If full delete:

```bash
git reset --hard HEAD~1
```

👉 Deletes commit and code changes

⚠ Never use `--hard` on shared branches.

---

# 🔹 D) REBASE (Update Branch)

If develop has new changes:

```bash
git checkout feature/branch-name
git fetch origin
git rebase origin/develop
```

👉 Moves your branch on top of latest develop.

Why use rebase?
- Keeps history clean
- Avoids merge commit

After rebase:

```bash
git push --force
```

Because rebase changes history.

---

# 🔹 E) CHERRY-PICK

Used to copy one specific commit.

```bash
git log
```

Copy commit hash.

```bash
git checkout feature/other-branch
git cherry-pick <commit-hash>
```

👉 Used when:
- Need one bug fix in another branch
- Don't want full branch changes

---

# ✅ PART 3 — Real Scenario

---

## 1️⃣ Create New Feature Branch

```bash
git checkout develop
git checkout -b feature/first-feature
```

---

## 2️⃣ Add Commit Message Hook

Create file:

```bash
touch .git/hooks/commit-msg
```

Add this code inside:

```bash
#!/bin/sh
grep -q "TP2-" "$1" || {
  echo "Commit must contain TP2-XXXX"
  exit 1
}
```

Make executable:

```bash
chmod +x .git/hooks/commit-msg
```

👉 Purpose:
- Ensures commit message contains task ID
- Prevents bad commit messages

---

## 3️⃣ Perform Commits

```bash
echo "A" >> test.txt
git add .
git commit -m "TP2-101 Added A"

echo "B" >> test.txt
git add .
git commit -m "TP2-101 Added B"
```

---

## 4️⃣ Create Pull Request

```bash
git push origin feature/first-feature
```

Then:
- Go to GitHub
- Create PR
- Base branch: develop

---

## 5️⃣ Keep PR Small

Best practice:
- 1 feature
- 1 clean commit

If multiple commits:

```bash
git rebase -i develop
```

Use squash.

---

## 6️⃣ Create Second Feature While First PR in Review

```bash
git checkout develop
git checkout -b feature/second-feature
```

Add commit:

```bash
echo "Second feature" >> second.txt
git add .
git commit -m "TP2-202 Added second feature"
git push origin feature/second-feature
```

---

## 7️⃣ Update Branch After Previous PR Merged

```bash
git checkout feature/second-feature
git fetch origin
git rebase origin/develop
git push --force
```

👉 Keeps branch updated and clean.

---

## 🔟 Add Version Tag

```bash
git tag v1.0.0
git push origin v1.0.0
```

👉 Used to track releases and builds.

---

## 1️⃣1️⃣ Create 3rd & 4th Branch

```bash
git checkout develop
git checkout -b feature/third

git checkout develop
git checkout -b feature/fourth
```

Add commit in third:

```bash
echo "Updated README" >> README.md
git add .
git commit -m "TP2-303 Updated README"
git push
```

---

## 1️⃣2️⃣ Cherry-Pick to 4th Branch

```bash
git log
git checkout feature/fourth
git cherry-pick <commit-hash>
```

---

## 1️⃣3️⃣ Change Commit Message

Latest commit:

```bash
git commit --amend -m "TP2-303 Updated README properly"
```

Older commit:

```bash
git rebase -i HEAD~2
```

Change `pick` to `reword`.

---

## 1️⃣4️⃣ Add 3 Commits and Delete Last

```bash
echo "1" >> a.txt
git commit -am "Commit 1"

echo "2" >> a.txt
git commit -am "Commit 2"

echo "3" >> a.txt
git commit -am "Commit 3"
```

Delete last commit:

```bash
git reset --hard HEAD~1
```

---

# 🔥 Which Command to Use When

| Situation | Command |
|------------|----------|
| Clean commits before PR | `git rebase -i` (squash) |
| Update branch with develop | `git rebase` |
| Copy one commit | `git cherry-pick` |
| Undo last commit | `git reset` |
| Edit commit message | `--amend` |
| Track release | `git tag` |
| Enforce commit rule | Git Hook |

---

# 🎯 What Interviewer Is Testing

They want to check if you understand:

- Clean Git history
- Small PR strategy
- Rebase vs Merge
- Cherry-pick usage
- Reset types
- Tagging releases
- Hook validation
- Proper branching strategy

---

# 📌 Final Note

Follow these best practices:

✔ One feature = One branch  
✔ One PR = One clean commit  
✔ Always rebase before PR  
✔ Never force push main/develop  
✔ Always include task ID in commit  

---

End of Git Practical Guide 🚀
