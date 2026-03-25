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

To automatically use the correct Git name and email depending on which folder you're in, we use Git's `includeIf` directive in the global `~/.gitconfig`.

### Global `.gitconfig`
In your `~/.gitconfig`, standard values are used. But for any repository under a specific directory (like your Personal folder), an override file is loaded:

```ini
[includeIf "gitdir:C:/Users/Nuaav01/Desktop/Personal/"]
    path = ~/.gitconfig-personal
```

### Personal Override `.gitconfig-personal`
This override file sets your personal commit overrides:

```ini
[user]
    name = Your Personal Name
    email = personal@example.com
```

### How it works:
Whenever you initialize or clone a repository inside `C:/Users/Nuaav01/Desktop/Personal/`, Git automatically evaluates the `includeIf` directive and applies the settings from `~/.gitconfig-personal`. This completely prevents accidental commits using your work email on personal repositories!

## 3. Workflow Example

1. Ensure your repository is inside the defined directory (e.g., `Desktop/Personal/`).
2. Initialize or clone using your preferred SSH hostname:
   `git remote add origin git@github.com:yourusername/yourrepo.git`
3. Commit normally. The correct SSH key handles authentication and the correct `.gitconfig` handles the author metadata automatically!
