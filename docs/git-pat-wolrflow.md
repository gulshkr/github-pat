# Git + Azure DevOps PAT Workflow

## 1️⃣ Create a Personal Access Token (PAT)
1. Sign in to Azure DevOps as **EXT_Yash.Sharma@code.com**.
2. Click your avatar → **User Settings → Personal access tokens** → **+ New Token**.
3. **Scope**: `Code → Read & Write` (add other scopes if needed).
4. **Expiration**: choose a period (e.g., 30 days).
5. Click **Create** and **copy** the token – you will see it only once.

## 2️⃣ Configure Git to store credentials (once per machine)
```powershell
# Use the built‑in Git Credential Manager Core
git config --global credential.helper manager-core
```
The first time you authenticate, the PAT will be saved securely in the Windows Credential Manager.

## 3️⃣ Clone a repository (HTTPS)
### 3‑a Interactive (recommended for first use)
```powershell
git clone https://dev.azure.com/landingzone/Project%20Horizon/_git/code-infra
```
When prompted:
- **Username**: `EXT_Yash.Sharma@code.com`
- **Password**: *paste the PAT*

### 3‑b One‑liner (useful for scripts)
```powershell
$pat = "<YOUR_PAT>"
$enc = [System.Web.HttpUtility]::UrlEncode($pat)
git clone "https://$enc@dev.azure.com/landingzone/Project%20Horizon/_git/code-infra"
```
Replace `<YOUR_PAT>` with the token you generated.

## 4️⃣ Pull latest changes
```powershell
cd code-infra
git pull
```
If the credential manager has the PAT, this works silently.

## 5️⃣ Push your changes
```powershell
# After committing locally
git push origin <branch-name>
```
If authentication fails, erase the stored credential and try again:
```powershell
git credential-manager-core erase
```
Then run any Git command; you’ll be prompted for the PAT again.

## 6️⃣ Updating / Revoking the PAT
1. **Delete the stored credential** (Windows Credential Manager → `git:https://dev.azure.com`).
2. **Create a new PAT** in Azure DevOps (repeat step 1).
3. The next Git operation will ask for the new PAT and store it.

## 7️⃣ Quick cheat‑sheet
| Action | Command | Notes |
|--------|---------|-------|
| Clone (interactive) | `git clone https://dev.azure.com/landingzone/Project%20Horizon/_git/code-infra` | Username = email, Password = PAT |
| Clone (script) | `$pat="<PAT>"; $enc=[System.Web.HttpUtility]::UrlEncode($pat); git clone "https://$enc@dev.azure.com/landingzone/Project%20Horizon/_git/code-infra"` | No credential manager needed |
| Pull | `git pull` | Run inside repo |
| Push | `git push origin <branch>` | Replace `<branch>` |
| Erase stored PAT | `git credential-manager-core erase` | Forces re‑prompt |
| Set credential helper | `git config --global credential.helper manager-core` | Saves PAT securely |

---
*Keep this file (`git-pat-workflow.md`) in the `docs/` folder of your Terraform‑Azure project for future reference.*
