# Setting Up GitHub on a Linux machine

So, you have a new (linux) machine, and you need to configure it to access your existing GitHub account for `git` command on terminal.

### Assumptions:
- Some knowledge of Linux and git
- Linux is already installed and functional on the machine
- Ubuntu / Debian or Fedora / RHEL based Linux distro

## 1. Install Git (if not already installed)

First, let's make sure Git is installed on your system:

```bash
sudo apt update
sudo apt install git
```

For Fedora/RHEL-based distributions:
```bash
sudo dnf install git
```

## 2. Configure Git with your identity

Set up your name and email (use the same email you used for your GitHub account):

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

## 3. Generate an SSH key

SSH keys provide a secure way to authenticate with GitHub:

```bash
ssh-keygen -t ed25519 -C "your.email@example.com"
```

Just press Enter to accept the default file location. You can set a passphrase for added security or press Enter for no passphrase.

## 4. Start the SSH agent and add your key

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

## 5. Copy your SSH public key

```bash
cat ~/.ssh/id_ed25519.pub
```
This will display your public key - select and copy it.

_Pro Tip: there is a Linux utility called `xclip`, which works like `pbcopy` in MacOS. You can directly copy the contents of a file into clipboard. Give it a try!_


## 6. Add the SSH key to your GitHub account

1. Log in to GitHub in your browser
2. Click on your profile picture in the top-right corner
3. Select "Settings"
4. Click on "SSH and GPG keys" in the left sidebar
5. Click "New SSH key"
6. Add a title (e.g., "Linux Laptop")
7. Paste your public key into the "Key" field
8. Click "Add SSH key"

## 7. Test your SSH connection

```bash
ssh -T git@github.com
```

You might see a warning about authenticity - type "yes" to continue. If successful, you should see a message like: "Hi username! You've successfully authenticated, but GitHub does not provide shell access."

## 8. Using Git with GitHub

Now you can clone repositories, push changes, etc.:

```bash
# Clone a repository
git clone git@github.com:username/repository.git

# Create a new repository locally and push to GitHub
mkdir my-project
cd my-project
git init
# Create some files
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin git@github.com:username/my-project.git
git push -u origin main
```

That's all!
