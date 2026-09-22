## 🚀 Git Remote Workflow for Your Notes

### 1. Initial Setup and First Push (The `-u` Setup)

> **Note:** The renaming part (`git branch -M main`) is not relevant if you set the default `main` name in your `.gitconfig` configuration file. 

To connect your local `Notes` repository to GitHub and publish its contents, you used three commands:

| Command                       | Purpose                                                                         | Authentication Detail                                                                                                                                            |
| :---------------------------- | :------------------------------------------------------------------------------ | :--------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `git remote add origin https://github.com/tomtor8/notes.git`   | Creates the remote link (alias `origin`).                                       |                                                                                                                                                                  |
| `git branch -M main`          | Renames the default branch to `main`.                                           |                                                                                                                                                                  |
| **`git push -u origin main`** | **First Push:** Uploads content and sets the **upstream tracking link** (`-u`). | This is when you were prompted to use your **Personal Access Token (PAT)** as your password for authentication, which your Linux credential manager then stored. |

### 2. Daily Routine (Push/Pull)

After the initial setup, your day-to-day workflow on the original computer becomes simple because the tracking link is already established:

| Command    | Purpose                                                                                |
| :--------- | :------------------------------------------------------------------------------------- |
| `git push` | Sends your new local commits (Markdown changes) to the remote `origin/main` on GitHub. |
| `git pull` | Retrieves any new changes from the remote `origin/main` to update your local copy.     |

### 3. Setting Up a New Computer (Cloning)

To get a copy of the repository onto a second computer (e.g., your Cachyos machine), you use one single command:

| Command               | Purpose                                                                                                    | Authentication Detail                                                                                       |
| :-------------------- | :--------------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------- |
| **`git clone [URL]`** | Downloads the **entire repository** (including history and branch setup) from GitHub to your new computer. | You will be prompted again for your GitHub username and **Personal Access Token (PAT)** for authentication. |

---

This sequence ensures your notes are synchronized across all your devices using secure token-based authentication.
