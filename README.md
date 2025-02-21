# Using Multiple GitHub Accounts in VS Code

Managing multiple GitHub accounts in VS Code can be tricky. This guide explains how to configure SSH keys and Git settings for seamless switching between your **personal**, **company**, and **organization (ATX)** accounts.

---

## 1. Generate SSH Keys for Each Account  
Open a terminal and run:

```sh
ssh-keygen -t rsa -b 4096 -C "your_personal_email@example.com" -f ~/.ssh/id_rsa_personal
ssh-keygen -t rsa -b 4096 -C "your_company_email@example.com" -f ~/.ssh/id_rsa_company
```

This generates two key pairs:
- `id_rsa_personal` (Personal account)  
- `id_rsa_company` (Company & ATX Organization account)  

---

## 2. Add SSH Keys to SSH Agent  
Run:

```sh
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa_personal
ssh-add ~/.ssh/id_rsa_company
```

Then, configure your SSH settings:

```sh
nano ~/.ssh/config
```

Add:

```ini
# Personal GitHub
Host github.com-personal
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa_personal

# Company GitHub
Host github.com-company
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa_company

# ATX Organization
Host github.com-atxlabs
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa_company
```

Save and exit (`CTRL + X`, then `Y`, then `Enter`).

---

## 3. Add SSH Keys to GitHub  
For each account:

1. **Personal account:**  
   ```sh
   cat ~/.ssh/id_rsa_personal.pub
   ```
   Copy and add it to **GitHub → Settings → SSH Keys**.

2. **Company account & ATX Organization:**  
   ```sh
   cat ~/.ssh/id_rsa_company.pub
   ```
   Copy and add it to both **Company GitHub** and the **ATX Organization** under **GitHub → Settings → SSH Keys**.

---

## 4. Clone Repositories with Correct SSH Host  
- **Personal projects:**  
  ```sh
  git clone git@github.com-personal:your-personal-username/repo-name.git
  ```

- **Company projects:**  
  ```sh
  git clone git@github.com-company:your-company-username/repo-name.git
  ```

- **ATX Organization projects:**  
  ```sh
  git clone git@github.com-atxlabs:ATXLabs/repo-name.git
  ```

---

## 5. Configure Git User Per Repository  
Inside **company project folder**, run:

```sh
cd /path/to/company/project
git config user.name "Your Company Name"
git config user.email "your_company_email@example.com"
```

Inside **personal project folder**, run:

```sh
cd /path/to/personal/project
git config user.name "Your Personal Name"
git config user.email "your_personal_email@example.com"
```

Inside **ATX Organization project folder**, run:

```sh
cd /path/to/atx/project
git config user.name "Your Company Name"
git config user.email "your_company_email@example.com"
```

Verify settings:

```sh
git config --get user.email
```

---

## 6. Add Remote Origin for ATX Organization  
If you have an existing project and want to push it to the **ATX Organization**, follow these steps:

1. **Navigate to the project folder:**

   ```sh
   cd /path/to/atx/project
   ```

2. **Initialize Git (if not already initialized):**

   ```sh
   git init
   ```

3. **Add ATX Organization as Remote Origin:**

   ```sh
   git remote add origin git@github.com-atxlabs:ATXLabs/repo-name.git
   ```

4. **Verify the Remote:**

   ```sh
   git remote -v
   ```

   Output should be:

   ```
   origin  git@github.com-atxlabs:ATXLabs/repo-name.git (fetch)
   origin  git@github.com-atxlabs:ATXLabs/repo-name.git (push)
   ```

5. **Add and Commit Changes:**

   ```sh
   git add .
   git commit -m "Initial commit"
   ```

6. **Push to ATX Organization Repository:**

   ```sh
   git push -u origin main
   ```

---

## 7. Automate Git User Switching  
Edit `~/.gitconfig`:

```sh
nano ~/.gitconfig
```

Add:

```ini
[includeIf "gitdir:/path/to/company/projects/"]
    path = ~/.gitconfig-company

[includeIf "gitdir:/path/to/personal/projects/"]
    path = ~/.gitconfig-personal

[includeIf "gitdir:/path/to/atx/projects/"]
    path = ~/.gitconfig-atx
```

Create `~/.gitconfig-company`:

```ini
[user]
    name = "Your Company Name"
    email = "your_company_email@example.com"
```

Create `~/.gitconfig-personal`:

```ini
[user]
    name = "Your Personal Name"
    email = "your_personal_email@example.com"
```

Create `~/.gitconfig-atx`:

```ini
[user]
    name = "Your Company Name"
    email = "your_company_email@example.com"
```

Git will now **automatically** use the correct account based on the folder.

---

## 8. Verify SSH Authentication  
Run:

```sh
ssh -T git@github.com-personal
ssh -T git@github.com-company
ssh -T git@github.com-atxlabs
```

Expected output:

✅ **Personal:** `Hi your-personal-username! You've successfully authenticated.`  
✅ **Company:** `Hi your-company-username! You've successfully authenticated.`  
✅ **ATX Organization:** `Hi your-company-username! You've successfully authenticated.`  

---

Now, 
