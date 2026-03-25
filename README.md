# github-pat
github-pat, how you can push your code and all without configuring multiple github on your system.

## Overview Architecture

Below is a visual representation of how the directory-based identity (via Git) and authentication (via SSH) interact to form a seamless workflow:

```mermaid
flowchart TD
    subgraph "Your Projects"
        DirP[folder: Desktop/Personal/]
        DirW[folder: Desktop/Work/]
    end

    subgraph "Git Configuration (Name/Email)"
        GlobalConfig{{"Global ~/.gitconfig (includeIf)"}}
        GitP["~/.gitconfig-personal"]
        GitW["~/.gitconfig-work"]
    end

    subgraph "SSH Configuration (Authentication)"
        SSHConfig{{"~/.ssh/config"}}
        KeyP["Personal Key (github.com)"]
        KeyW["Work Key (github-work)"]
    end

    %% Git flows
    DirP --> GlobalConfig
    GlobalConfig -.->|If path matches| GitP
    
    DirW --> GlobalConfig
    GlobalConfig -.->|If path matches| GitW

    %% SSH flows
    DirP -.->|Push/Pull via remote alias| SSHConfig
    SSHConfig -.->|Alias: github.com| KeyP
    
    DirW -.->|Push/Pull via remote alias| SSHConfig
    SSHConfig -.->|Alias: github-work| KeyW
```

> [!TIP]
> **Infinite Expansion:** You are not limited to just "Work" and "Personal"! You can create infinitely multiple setups like this. If you get a new client or need a new proxy configuration, simply create a new directory (e.g., `Desktop/ClientX/`), add an `includeIf` block mapping it to `~/.gitconfig-clientx`, and create a new SSH host alias for it.
