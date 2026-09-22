# Pass CLI utility

The **`pass`** utility (also known as the "standard unix password manager") is a lightweight, command-line password manager that follows the Unix philosophy. It stores each password inside a GPG-encrypted file, organized within a simple folder hierarchy.

Here is a complete guide to setting it up and using it.

---

## Prerequisites & Installation

Before using `pass`, you need to have **GnuPG (GPG)** installed and a GPG key pair generated, as `pass` relies on it for encryption.

### Step A: Install `pass`

Install it using your distribution's package manager:

- **Arch Linux:** `sudo pacman -S pass`

### Step B: Generate a GPG Key (If you don't have one)

If you haven't set up a GPG key yet, run:

```bash
gpg --full-generate-key
```

Follow the prompts (the defaults, like RSA and 3072/4096 bits, are usually best). Take note of the **Email Address** or the **Key ID** you associated with this key.

---

## Initializing the Password Store

To start using `pass`, you must initialize it with your GPG key ID or email address:

```bash
pass init your.email@example.com
```

This creates a hidden directory at `~/.password-store/` where your encrypted credentials will live.

---

## Basic Usage (CRUD Operations)

### Insert a Password

To store a new password, use the `insert` command followed by the category and account name you want to create:

```bash
pass insert email/gmail
```

You will be prompted to enter and verify the password.

### Generate a Random Password

If you want `pass` to generate a secure, random password for you, use `generate`. Specify the path and the desired length (e.g., 20 characters):

```bash
pass generate social/facebook 20
```

_Tip: Add the `-n` flag if you want a password without symbols (alphanumeric only)._

### List Stored Passwords

To view your password structure in a neat tree format, simply type:

```bash
pass
```

**Example output:**

```plaintext
Password Store
├── email
│   └── gmail
└── social
    └── facebook
```

### Retrieve a Password

To view a password, pass its path:

```bash
pass email/gmail
```

You will be prompted for your GPG passphrase to decrypt it, and the password will be printed to the terminal.

> **note** Security tip
> To copy the password directly to your clipboard without printing it to the screen, add the `-c` flag. It will automatically clear your clipboard after 45 seconds:

```bash
pass -c email/gmail
```

### Edit a Password File

Because `pass` files are just encrypted text files, you can store extra information (like usernames, URLs, or security questions) on subsequent lines. To edit a file:

```bash
pass edit email/gmail
```

This opens the decrypted file in your default text editor (like `nano` or `vim`). The actual password **must remain on the very first line**, and extra metadata goes below it.

### Delete a Password

To remove a password permanently:

```bash
pass rm email/gmail
```

---

## Advanced Features

### Git Integration

One of the best features of `pass` is its built-in Git tracking. It automatically commits changes every time you add, edit, or remove a password.

1.  **Initialize Git:**

```bash
pass git init
```

2.  **Add a remote repository** (e.g., a private GitHub/GitLab repo) to back up your sync:

```bash
pass git remote add origin git@github.com:username/pass-repo.git
```

3.  **Push/Pull your changes:**

```bash
pass git push
pass git pull
```

### Customize the PIN entry program

If you want to keep everything strictly inside your terminal without spawning any external graphical windows, you can force GPG to use the text-based interface. It renders directly inside your active terminal pane.

1. Install the curses package - on Arch it is bundled in `pinentry`.
2. Edit `~/.gnupg/gpg-agent.conf` and explicitly point to it:

```toml
pinentry-program /usr/bin/pinentry-curses
```

3. Apply the changes:

```bash
gpg-connect-agent reloadagent /bye
```

For pinentry-curses to know exactly which terminal pane or split screen to render inside, GPG needs to track your active TTY. If you ever run into an error where it complains about a missing terminal or fails to pop up, make sure you have this environment variable exported in your Fish configuration (~/.config/fish/config.fish):

```fish
set -gx GPG_TTY (tty)
```

# Export/import GPG keys

To import your GPG key onto the second device, you need to export both your **public key** and your **private (secret) key** from your first device, transfer them securely to the second device, and then import them.

Here is the step-by-step process.

---

## Step 1: Export the Keys from the First Device

On your **first (original) device**, open a terminal.

**Find your Key ID or Email:**

```bash
gpg --list-secret-keys --keyid-format=long
```

Look for the line starting with `sec`. The string of characters after the bits (e.g., `rsa3072/ABC123XYZ789...`) or the email address associated with it is your Key ID.

**Export the Public Key:**

```bash
gpg --export --armor your.email@example.com > mypublickey.asc
```

**Export the Private Key:**

> ⚠️ **Important:** Your private key is what decrypts your passwords. Keep this file completely secure and delete it from your transfer medium as soon as you are done.

```bash
gpg --export-secret-keys --armor your.email@example.com > myprivatekey.asc
```

---

## Step 2: Transfer the Files to the Second Device

Move `mypublickey.asc` and `myprivatekey.asc` to your second device.

Since you are already using `rclone` and `pcloud`, you can temporarily drop them into an encrypted folder there, or use a secure local method like a USB drive or `scp` over your local network.

---

## Step 3: Import the Keys on the Second Device

On your **second device**, open a terminal in the folder where you placed the `.asc` files and run the following commands:

1.  **Import the Public Key:**

    Bash

    ```
    gpg --import mypublickey.asc
    ```

2.  **Import the Private Key:**

    Bash

    ```
    gpg --import myprivatekey.asc
    ```

    _(You will be prompted to enter the passphrase you originally created for this key on the first device)._

3.  **Clean Up:**

    Securely delete the `myprivatekey.asc` file from both devices and any cloud storage to ensure no one else can access your secret key.

    Bash

    ```
    rm myprivatekey.asc mypublickey.asc
    ```

---

## Step 4: Trust the Key

By default, GPG marks imported private keys with "unknown" trust on a new system. You need to tell GPG that you explicitly trust your own key.

1.  Run the edit command:

    Bash

    ```
    gpg --edit-key your.email@example.com
    ```

2.  You will enter an interactive GPG prompt (`gpg>`). Type:

    Plaintext

    ```
    trust
    ```

3.  Enter **`5`** for "I trust ultimately" (since it is your own key).
4.  Confirm by typing **`y`**.
5.  Type **`quit`** to exit.

Your second device is now fully authorized to decrypt and use the `pass` files synced via `rclone`.
