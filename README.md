# Using Multiple GitHub Accounts in VS Code

Managing multiple GitHub accounts in VS Code can be tricky. This guide explains how to configure SSH keys and Git settings for seamless switching between your **personal** and **company** accounts.

---

## 1. Generate SSH Keys for Each Account
Open a terminal and run:

```sh
ssh-keygen -t rsa -b 4096 -C "your_personal_email@example.com" -f ~/.ssh/id_rsa_personal
ssh-keygen -t rsa -b 4096 -C "your_company_email@example.com" -f ~/.ssh/id_rsa_company
```

This generates two key pairs:
- `id_rsa_personal` (Personal account)
- `id_rsa_company` (Company account)

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

2. **Company account:**  
   ```sh
   cat ~/.ssh/id_rsa_company.pub
   ```
   Copy and add it to **GitHub → Settings → SSH Keys**.

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

Verify settings:

```sh
git config --get user.email
```

---

## 6. Automate Git User Switching
Edit `~/.gitconfig`:

```sh
nano ~/.gitconfig
```

Add:

```ini
[includeIf "gitdir:/path/to/company/projects/"]
    path = ~/.gitconfig-company
```

Then create `~/.gitconfig-company`:

```ini
[user]
    name = "Your Company Name"
    email = "your_company_email@example.com"
```

Now, Git will **automatically** use the correct account based on the folder.

---

## 7. Verify SSH Authentication
Run:

```sh
ssh -T git@github.com-personal
ssh -T git@github.com-company
```

Expected output:

✅ **Personal:** `Hi your-personal-username! You've successfully authenticated.`  
✅ **Company:** `Hi your-company-username! You've successfully authenticated.`

---

Now, VS Code will push and commit to the **correct** GitHub account based on the project folder. 🚀
