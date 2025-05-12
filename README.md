# Managing Multiple GitHub Accounts on One Machine (Linux, Windows, macOS)

This guide helps you set up and manage **multiple GitHub accounts** (e.g., personal and work) on a single machine using SSH keys and custom `.gitconfig` files.

---

## 🔐 1. Generate SSH Keys

Create separate SSH keys for each GitHub account:

### Personal Account
```bash
ssh-keygen -t ed25519 -C "your_personal_email@example.com"
# Save as: ~/.ssh/id_personal_github
```
### Work Account
```bash
ssh-keygen -t ed25519 -C "your_work_email@company.com"
# Save as: ~/.ssh/id_work_github
```
Add SSH Keys to the Agent
```
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_personal_github
ssh-add ~/.ssh/id_work_github
```
🌐 2. Add SSH Keys to GitHub

Step 1: Copy public key
```
cat ~/.ssh/id_personal_github.pub
```

Step 2: Add to GitHub
```
Go to GitHub → Settings → SSH and GPG keys → New SSH key:

Title: Personal Key

Paste the copied key

Repeat for the work key (id_work_github.pub).
```

⚙️ 3. Create .gitconfig Files
---
### Note: Make sure the username in your Git configuration matches your actual GitHub username. GitHub does not validate the username when using SSH keys, so if there’s a mismatch, it won’t show an error—but you might unintentionally push code under someone else’s account if that username exists.

Personal Folder (~/Code/personal/.gitconfig)
```
[user]
   email = your_personal_email@example.com
   name = personal_username
[github]
   user = "personal_username" # names should be in double quotes
[credentials]
   username = "personal_username"  # names should be in double quotes
[core]
   sshCommand = "ssh -i ~/.ssh/id_personal_github"
```
Work Folder (~/Code/work/.gitconfig)
```
[user]
   email = your_work_email@company.com
   name = work_username
[github]
   user = "work_username"  # names should be in double quotes
[credentials]
   username = "work_username"  # names should be in double quotes
[core]
   sshCommand = "ssh -i ~/.ssh/id_work_github"
```
🗃️ 4. Global .gitconfig (in ~/.gitconfig)
```
# Default account - Personal
[includeIf "gitdir:~/Code/personal/"]
    path = ~/Code/personal/.gitconfig

# Work account - Organization
[includeIf "gitdir:~/Code/work/"]
    path = ~/Code/work/.gitconfig

[core]
    excludesfile = ~/.gitignore
```
✅ 5. Verify the Active GitHub Account
To confirm which GitHub account is being used:

Clone a repository using SSH from GitHub (either personal or work):

bash
```
git clone git@github.com:username/repo-name.git
```
Paste the cloned repo into both your personal and work folders (e.g., ~/Code/personal/ and ~/Code/work/).

In each folder:

Make a small change.

Commit the change:

```
git commit -am "Test commit"
```
Run the following to verify the identity:

```
git config user.name
git config user.email
```
Confirm that the name and email match the values defined in the .gitconfig file specific to that folder.

This ensures the correct GitHub account is being used with the corresponding SSH key and identity settings.

📁 Folder Structure Example
```
~
├── .ssh/
│   ├── id_personal_github
│   ├── id_personal_github.pub
│   ├── id_work_github
│   └── id_work_github.pub
├── Code/
│   ├── personal/
│   │   └── .gitconfig
│   └── work/
│       └── .gitconfig
└── .gitconfi
```
