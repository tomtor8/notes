# Initial setup of GIT

> **Note:** You should install the `less` utility because `git` uses it for displaying its output.

## 🛠️ Step 1: Install Git (If Not Already Installed)

Although Git is often pre-installed or included in initial setup scripts on many distributions, you should ensure it's on your system.

On an **Arch-based system** like CachyOS, you'd use the `pacman` package manager:

```bash
sudo pacman -S git
```

You can verify the installation by checking the version:

```bash
git --version
```

---

## 👤 Step 2: Configure Your User Identity

The two most crucial settings are your **name** and **email address**. Git uses this information to label the commits you create. This information is embedded directly into your commit history, so use the name and email you want publicly associated with your work.

### 1\. Set Your Name

```bash
git config --global user.name "Your Full Name"
```

Replace `"Your Full Name"` with your actual name.

### 2\. Set Your Email

```bash
git config --global user.email "your.email@example.com"
```

Replace `"your.email@example.com"` with your actual email address.

> **Note:** The **`--global`** flag means these settings will apply to _every_ Git repository on your system. If you need a different identity for a specific project (e.g., a work vs. personal project), you can run the commands _without_ the `--global` flag inside that project's directory.

---

## 🎨 Step 3: Configure Basic Preferences

For a better experience in the terminal, you can set a few helpful defaults.

### 1\. Set a Default Branch Name

Many people now prefer to use **`main`** instead of the historical default **`master`** for the initial branch name.

```bash
git config --global init.defaultBranch main
```

### 2\. Configure Your Default Text Editor

Since your favorite text editor is **Neovim** (specifically Astronvim), you should configure Git to use it when you need to write commit messages, merge messages, or tags.

```bash
git config --global core.editor "nvim"
```

> **Tip:** If you use an editor that runs inside a terminal (like **Neovim** or **Nano**), you might also want to add the **`--wait`** or **`--cli`** flags to ensure Git waits for the editor to close before continuing, depending on how your specific editor setup works. For plain `nvim`, simply using `"nvim"` is usually sufficient.

### 3\. Enable Color Output (Recommended)

This makes the output of commands like `git status` and `git diff` much easier to read.

```bash
git config --global color.ui auto
```

---

## ✅ Step 4: Verify Your Configuration

You can view all your current global settings by listing the configuration file:

```bash
git config --global --list
```

This should show the details you just entered, similar to this:

```
user.name=Your Full Name
user.email=your.email@example.com
init.defaultbranch=main
core.editor=nvim
color.ui=auto
```

If you want to use `nvim` as a git difftool tool add these changes to your `.gitconfig` file:

```sh
git config --global diff.tool nvimdiff
git config --global difftool.prompt false
```

If you run `git difftool` it will launch neovim in side-by-side mode.
