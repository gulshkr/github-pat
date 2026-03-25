# Managing Multiple Git Accounts

When working with multiple GitHub accounts (for example, one for work and one for personal projects) on the same machine, it can be tricky to ensure that your commits use the correct email address and SSH key.

This guide explains the setup we use to automatically switch between personal and work accounts.

## 1. Multiple SSH Keys

First, generate separate SSH keys for your work and personal accounts:
- `~/.ssh/id_ed25519_personal` (Personal)
- `~/.ssh/id_ed25519_work` (Work)

Add these to your `~/.ssh/config` file to map them to different Host aliases:

```ssh-config
# Personal GitHub
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_personal
    StrictHostKeyChecking no

# Work GitHub
Host github-work
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_work
```

With this setup:
- A clone URL like `git@github.com:username/personal-repo.git` uses your **Personal** key.
- A clone URL like `git@github-work:username/work-repo.git` uses your **Work** key.

## 2. Directory-Based Git Configurations

To automatically use the correct Git name and email depending on which folder you're in, we use Git's `includeIf` directive in the global `~/.gitconfig`. We set up two specific folders: `Desktop/Personal/` and `Desktop/Work/`.

### Step 1: Update Global `.gitconfig`
In your global `~/.gitconfig` file, add the directory mappings pointing to respective override files. This tells Git to use different settings based on your current path:

```ini
[includeIf "gitdir:C:/Users/Nuaav01/Desktop/Personal/"]
    path = ~/.gitconfig-personal

[includeIf "gitdir:C:/Users/Nuaav01/Desktop/Work/"]
    path = ~/.gitconfig-work
```

### Step 2: Create Personal Override (`~/.gitconfig-personal`)
Create this file to define your personal identity. Any repository inside the `Desktop/Personal/` directory will strictly use this:

```ini
[user]
    name = Your Personal Name
    email = personal@example.com
```

### Step 3: Create Work Override (`~/.gitconfig-work`)
Create this file to define your official work identity. Any repository inside the `Desktop/Work/` directory will strictly use this:

```ini
[user]
    name = Your Work Name
    email = work@code.com
```

### How it works:
Whenever you run a git command, Git automatically checks your current path. If you are inside `Desktop/Work/`, it applies the `~/.gitconfig-work` settings. If you are in `Desktop/Personal/`, it applies your personal settings. **This entirely prevents accidental commits using your work email on personal repositories!**

## 3. Workflow Example

1. **Working on Personal Code:**
   - Place your project strictly in `Desktop/Personal/`.
   - Add/Clone remote using the personal SSH alias: `git remote add origin git@github.com:username/personal-repo.git`
   - **Outcome:** Git seamlessly deploys your personal SSH key for authentication and logs commits under your personal Name/Email.

2. **Working on Work Code:**
   - Place your project strictly in `Desktop/Work/`.
   - Add/Clone remote using the work SSH alias: `git remote add origin git@github-work:company/work-repo.git`
   - **Outcome:** Git seamlessly deploys your work SSH key for authentication and logs commits under your official work Name/Email.
