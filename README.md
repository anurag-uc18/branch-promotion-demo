# Git Dev → Test → Prod Cheat Sheet

This file is a beginner-friendly reference for setting up and using a simple
Dev → Test → Prod workflow with Git and GitHub on a Mac mini.

---

# 1. The Basic Idea

Use **one GitHub repository** with three branches:

```text
dev   → Development
test  → Testing / QA
prod  → Production
```

Code is promoted in this direction:

```text
feature branch
      ↓
     dev
      ↓
    test
      ↓
    prod
```

The same committed code moves forward through the environments.

You do **not** manually copy files between Dev, Test, and Prod.

---

# 2. Recommended Folder Layout

On the Mac mini:

```text
~/GitHub/
├── myapp-dev
├── myapp-test
└── myapp-prod
```

Each folder is a separate clone of the same GitHub repository.

Each one permanently tracks a different branch:

```text
~/GitHub/myapp-dev   → dev
~/GitHub/myapp-test  → test
~/GitHub/myapp-prod  → prod
```

This lets all three versions exist at the same time.

For example:

```text
DEV   → localhost:3000
TEST  → localhost:4000
PROD  → localhost:5000
```

---

# 3. Important Git Vocabulary

## Repository

A repository, or **repo**, is the project Git is tracking.

Example:

```text
branch-promotion-demo
```

A GitHub repository is the remote copy hosted on GitHub.

---

## Branch

A branch is a version or line of development inside the same repository.

Example:

```text
dev
test
prod
```

Branches can contain different versions of the same files.

---

## Commit

A commit is a saved checkpoint.

Example:

```bash
git commit -m "Add login page"
```

Think of it as:

```text
Save this exact version of my work.
```

---

## Push

Push sends your local commits to GitHub.

```bash
git push
```

Think:

```text
Mac → GitHub
```

---

## Pull

Pull gets changes from GitHub and updates your local copy.

```bash
git pull
```

Think:

```text
GitHub → Mac
```

---

## Merge

Merge combines the changes from one branch into another.

Example:

```bash
git merge dev
```

If you are currently on `test`, this means:

```text
Bring the changes from dev into test.
```

---

## Checkout / Switch

Changes which branch you are working on.

Older syntax:

```bash
git checkout dev
```

Newer syntax:

```bash
git switch dev
```

Both work.

---

## Clone

Clone downloads a GitHub repository to your Mac.

```bash
git clone <repository-url>
```

or with GitHub CLI:

```bash
gh repo clone username/repository-name
```

---

# 4. Check That Git and GitHub CLI Are Installed

Check Git:

```bash
git --version
```

Check GitHub CLI:

```bash
gh --version
```

Check your GitHub login:

```bash
gh auth status
```

If you are not logged in:

```bash
gh auth login
```

---

# 5. Create a New Practice Repository

Create a folder:

```bash
mkdir -p ~/GitHub/branch-promotion-demo
cd ~/GitHub/branch-promotion-demo
```

Initialize Git:

```bash
git init
```

Create the production branch:

```bash
git checkout -b prod
```

Create a README:

```bash
echo "# Dev → Test → Prod Demo" > README.md
```

Stage the file:

```bash
git add README.md
```

Commit it:

```bash
git commit -m "Initial project setup"
```

Create a private GitHub repository and push it:

```bash
gh repo create branch-promotion-demo \
  --private \
  --source=. \
  --remote=origin \
  --push
```

---

# 6. Create the Dev and Test Branches

Create Test from Prod:

```bash
git checkout -b test
git push -u origin test
```

Create Dev from Test:

```bash
git checkout -b dev
git push -u origin dev
```

List branches:

```bash
git branch
```

Expected:

```text
* dev
  prod
  test
```

The `*` indicates your current branch.

---

# 7. Create a File in Dev

Make sure you are on Dev:

```bash
git checkout dev
```

Create a file:

```bash
nano tutorial.md
```

Example contents:

```markdown
# Dev → Test → Prod Tutorial

This file was created in Dev.

Promotion path:

DEV → TEST → PROD
```

In nano:

```text
Control + O   → Save
Enter         → Confirm filename
Control + X   → Exit
```

Check Git status:

```bash
git status
```

Stage the file:

```bash
git add tutorial.md
```

Commit it:

```bash
git commit -m "Add tutorial file"
```

Push it to GitHub:

```bash
git push origin dev
```

At this point:

```text
DEV   → has tutorial.md
TEST  → does not
PROD  → does not
```

---

# 8. Promote Dev → Test

Switch to Test:

```bash
git checkout test
```

Get the latest Test branch from GitHub:

```bash
git pull
```

Merge Dev into Test:

```bash
git merge dev
```

Push Test:

```bash
git push origin test
```

Now:

```text
DEV   → has tutorial.md
TEST  → has tutorial.md
PROD  → does not
```

---

# 9. Promote Test → Prod

Switch to Prod:

```bash
git checkout prod
```

Get the latest Prod branch:

```bash
git pull
```

Merge Test into Prod:

```bash
git merge test
```

Push Prod:

```bash
git push origin prod
```

Now:

```text
DEV   → has tutorial.md
TEST  → has tutorial.md
PROD  → has tutorial.md
```

This is the basic promotion workflow.

---

# 10. View the Commit History

Run:

```bash
git log --oneline --all --graph --decorate
```

This shows:

- commits
- branches
- where each branch currently points
- how changes moved through the repo

Example:

```text
* a1b2c3d (dev, test, prod) Add tutorial file
* e4f5g6h Initial project setup
```

If all three branches point to the same commit, all three currently contain
the same code.

---

# 11. Create Three Separate Local Copies

If you want Dev, Test, and Prod to exist simultaneously on the Mac mini, use
three clones.

Assume the repository is:

```text
username/branch-promotion-demo
```

Clone Dev:

```bash
gh repo clone username/branch-promotion-demo \
  ~/GitHub/demo-dev \
  -- --branch dev
```

Clone Test:

```bash
gh repo clone username/branch-promotion-demo \
  ~/GitHub/demo-test \
  -- --branch test
```

Clone Prod:

```bash
gh repo clone username/branch-promotion-demo \
  ~/GitHub/demo-prod \
  -- --branch prod
```

Result:

```text
~/GitHub/
├── demo-dev
├── demo-test
└── demo-prod
```

---

# 12. Verify Which Branch Each Folder Uses

Run:

```bash
echo "=== DEV ==="
git -C ~/GitHub/demo-dev branch --show-current

echo "=== TEST ==="
git -C ~/GitHub/demo-test branch --show-current

echo "=== PROD ==="
git -C ~/GitHub/demo-prod branch --show-current
```

Expected:

```text
=== DEV ===
dev

=== TEST ===
test

=== PROD ===
prod
```

The `-C` option tells Git:

```text
Run this Git command as if I were inside this folder.
```

---

# 13. Update the Three Deployment Copies

After pushing a new Dev change:

```bash
git -C ~/GitHub/demo-dev pull
```

After promoting Dev → Test:

```bash
git -C ~/GitHub/demo-test pull
```

After promoting Test → Prod:

```bash
git -C ~/GitHub/demo-prod pull
```

This keeps the three local copies synchronized with GitHub.

---

# 14. Run the Three Copies as Simple Websites

For a simple demonstration, Python can serve each folder.

## DEV

```bash
cd ~/GitHub/demo-dev
python3 -m http.server 3000
```

Open:

```text
http://localhost:3000
```

## TEST

In another Terminal tab:

```bash
cd ~/GitHub/demo-test
python3 -m http.server 4000
```

Open:

```text
http://localhost:4000
```

## PROD

In another Terminal tab:

```bash
cd ~/GitHub/demo-prod
python3 -m http.server 5000
```

Open:

```text
http://localhost:5000
```

Now all three versions are running independently.

---

# 15. Typical Daily Workflow

Suppose you are starting a new feature.

Switch to Dev:

```bash
git checkout dev
```

Make sure Dev is current:

```bash
git pull
```

Create a feature branch:

```bash
git checkout -b feature/new-login
```

Make your code changes.

Check what changed:

```bash
git status
```

Stage everything:

```bash
git add .
```

Commit:

```bash
git commit -m "Add new login page"
```

Push the feature branch:

```bash
git push -u origin feature/new-login
```

Then merge the feature into Dev:

```bash
git checkout dev
git pull
git merge feature/new-login
git push origin dev
```

Update the Dev deployment:

```bash
git -C ~/GitHub/myapp-dev pull
```

Test it.

When Dev looks good, promote it to Test:

```bash
git checkout test
git pull
git merge dev
git push origin test
```

Update Test:

```bash
git -C ~/GitHub/myapp-test pull
```

Test again.

When Test passes, promote it to Prod:

```bash
git checkout prod
git pull
git merge test
git push origin prod
```

Update Prod:

```bash
git -C ~/GitHub/myapp-prod pull
```

---

# 16. Useful Commands

## Where am I?

```bash
pwd
```

---

## What files are here?

```bash
ls
```

Detailed listing:

```bash
ls -lrt
```

---

## What branch am I on?

```bash
git branch --show-current
```

or:

```bash
git status
```

---

## Show all local branches

```bash
git branch
```

---

## Show local and remote branches

```bash
git branch -a
```

---

## See what changed

```bash
git status
```

---

## See the actual line-by-line changes

```bash
git diff
```

---

## See recent commits

```bash
git log --oneline
```

---

## See branches visually

```bash
git log --oneline --all --graph --decorate
```

---

## Download GitHub changes

```bash
git pull
```

---

## Upload local commits

```bash
git push
```

---

# 17. Very Important Rules

## Rule 1: Always check your branch first

Before editing:

```bash
git branch --show-current
```

Do not assume you are on Dev.

---

## Rule 2: Pull before merging

Before promoting:

```bash
git checkout test
git pull
git merge dev
```

and:

```bash
git checkout prod
git pull
git merge test
```

---

## Rule 3: Do not develop directly in Prod

Avoid:

```bash
git checkout prod
nano app.py
```

Development should normally happen in:

```text
feature/* → dev → test → prod
```

---

## Rule 4: Do not manually edit deployment folders

Folders such as:

```text
~/GitHub/myapp-test
~/GitHub/myapp-prod
```

should normally be updated using:

```bash
git pull
```

rather than manually editing files there.

---

## Rule 5: Commit before switching branches

Before changing branches, check:

```bash
git status
```

Ideally it should say:

```text
nothing to commit, working tree clean
```

---

# 18. Git Status Explained

If:

```bash
git status
```

shows:

```text
Untracked files
```

Git sees a new file but is not tracking it yet.

Use:

```bash
git add filename
```

---

If it shows:

```text
Changes not staged for commit
```

Git tracks the file but you changed it.

Stage it:

```bash
git add filename
```

---

If it shows:

```text
Changes to be committed
```

the changes are staged and ready to commit.

Run:

```bash
git commit -m "Describe what changed"
```

---

If it shows:

```text
nothing to commit, working tree clean
```

your local files match your latest commit.

---

# 19. The Three Git Stages

A useful mental model:

```text
Working files
     │
     │ git add
     ▼
Staging area
     │
     │ git commit
     ▼
Local Git history
     │
     │ git push
     ▼
GitHub
```

Example:

```bash
git add tutorial.md
git commit -m "Update tutorial"
git push
```

means:

```text
Take tutorial.md
       ↓
prepare it for saving
       ↓
save a Git checkpoint
       ↓
upload the checkpoint to GitHub
```

---

# 20. Dev → Test → Prod Mental Model

Think of the environments as gates:

```text
DEV
│
│ Does it work for the developer?
▼
TEST
│
│ Does it pass testing?
▼
PROD
│
│ Stable version used by users
▼
Users
```

Git branches control which code belongs at each gate.

Separate Mac folders allow all three branches to run at the same time.

---

# 21. Current Demo Layout

The demo created on the Mac mini uses:

```text
~/GitHub/
├── demo-dev
├── demo-test
└── demo-prod
```

with branches:

```text
demo-dev   → dev
demo-test  → test
demo-prod  → prod
```

The simple web-server ports are:

```text
DEV   → 3000
TEST  → 4000
PROD  → 5000
```

---

# 22. Quick Promotion Cheat Sheet

## Dev → Test

```bash
git checkout test
git pull
git merge dev
git push origin test
```

Then:

```bash
git -C ~/GitHub/myapp-test pull
```

---

## Test → Prod

```bash
git checkout prod
git pull
git merge test
git push origin prod
```

Then:

```bash
git -C ~/GitHub/myapp-prod pull
```

---

# 23. Recommended Next Step

Once this manual process is comfortable, automate deployment with GitHub Actions:

```text
push dev
   ↓
automatically update Dev

push test
   ↓
automatically update Test

push prod
   ↓
automatically update Prod
```

Do the manual process first until the Git concepts are clear.

Automation becomes much easier once you understand what it is automating.
