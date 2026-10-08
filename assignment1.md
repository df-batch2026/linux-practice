# Assignment: Git, GitHub & Shell Scripting — "DevOps Journey" Repo

**Time:** 4-5 hours | **Total:** 100 points (+15 bonus)

**Goal:** Use Git and GitHub for real work while writing shell scripts. Every script you write goes into your repo through proper branches, commits and pull requests.

**Rule:** All work must be done in a single public GitHub repo named `devops-journey`. Do not copy-paste someone else's repo. Your commit history is your proof.

---

## Part 1: Local Git Basics (10 pts)

1. Install Git and set your identity:
   ```bash
   git config --global user.name "Your Name"
   git config --global user.email "you@example.com"
   ```
2. Create a folder `devops-journey`, run `git init`.
3. Add `README.md` (your name + one-line goal). Commit with a clear message.
4. Create `notes/linux.md`, `notes/git.md`, `notes/shell.md`. Commit each **separately**.
5. Add a `.gitignore` that ignores `*.log`, `.env`, and `backups/`. Create a dummy `.env` and confirm with `git status` that it is ignored.
6. Use `git diff` before at least 2 commits.

## Part 2: Shell Scripting Fundamentals (25 pts)

Create a `scripts/` folder. Write each script on its **own branch** `feature/<script-name>`, commit, then merge to `main` (see Part 3).

Every script must:
- Start with `#!/bin/bash`
- Have a comment header (what it does, usage, author)
- Be executable (`chmod +x`)
- Use `set -euo pipefail`
- Quote variables properly

### Script 1: `hello.sh` (basics)
- Take a name as argument (`$1`). If missing, print usage and `exit 1`.
- Print: `Hello <name>, today is <date>, you are logged in as <user>`.

### Script 2: `sysinfo.sh` (commands + output)
Print a clean report with: hostname, OS version, uptime, current user, disk usage of `/`, free memory, and top 3 memory-consuming processes. Save output to `sysinfo-<date>.log` when run with the flag `--save`.

### Script 3: `backup.sh` (loops, conditions, functions)
- Takes a source directory as argument.
- Checks the directory exists, otherwise exits with an error message.
- Creates a `.tar.gz` of it in `backups/` named `<dirname>-YYYYMMDD-HHMM.tar.gz`.
- Keeps only the **last 5 backups** (delete older ones).
- Uses at least one function and prints a success message with the file size.

### Script 4: `logscan.sh` (text processing)
Given a log file (create a sample `sample.log` with 30+ lines of mixed `INFO`, `WARN`, `ERROR`):
- Count lines for each level using `grep`/`awk`.
- Print the 5 most recent `ERROR` lines.
- Print the unique IP addresses found (use `grep -oE`, `sort`, `uniq -c`).

### Script 5: `healthcheck.sh` (real-world)
- Read a list of URLs from `urls.txt` (one per line, at least 5 URLs).
- Loop through each using `curl -s -o /dev/null -w "%{http_code}"`.
- Print `UP` for 200, `DOWN` otherwise, with response code.
- Exit with code 1 if any URL is down.

## Part 3: Branching & Merging (10 pts)

1. For each script above, use a branch like `feature/backup-script`. Commit at least twice per branch (e.g. first version, then fixes).
2. Merge each branch into `main` and delete it afterwards.
3. Run `git log --oneline --graph --all`, save a screenshot as `docs/graph.png`, and commit it.

## Part 4: GitHub & Pull Requests (15 pts)

1. Create an **empty** public repo `devops-journey` on GitHub (no README).
2. Connect and push:
   ```bash
   git remote add origin <url>
   git push -u origin main
   ```
3. Set up authentication using an SSH key (preferred) or a Personal Access Token.
4. Create branch `feature/improve-healthcheck`. Add a `--timeout` option to `healthcheck.sh`. Push the branch.
5. Open a **Pull Request** with a proper title and description (what changed, how to test).
6. Merge the PR on GitHub, then `git pull` on `main` locally.

## Part 5: Conflict Resolution (10 pts)

1. Create branch `conflict-a`. Change the header comment (line 2) of `hello.sh`. Commit.
2. Switch to `main`. Change the same line differently. Commit.
3. Merge `conflict-a` into `main`. A conflict will appear.
4. Resolve it manually, commit with message `Resolve merge conflict in hello.sh`, and push.

## Part 6: Undoing Mistakes (10 pts)

1. Make a bad commit (e.g. add a script with a typo), then undo it with `git revert`.
2. Stage a change, then unstage it with `git restore --staged <file>`.
3. In `notes/git.md`, explain in your own words the difference between `reset`, `revert` and `restore`.

## Part 7: Automate Git with Shell (20 pts)

This is where Git and shell scripting meet.

### Script 6: `gitstats.sh`
Run inside any git repo and print:
- Current branch name
- Total number of commits
- Number of commits per author (`git shortlog -sn`)
- Last 5 commit messages
- Number of modified/untracked files
- Warn if there are uncommitted changes

### Script 7: `quicksave.sh`
Usage: `./quicksave.sh "commit message"`
- Fails if no message is given.
- Runs `git add -A`, `git commit -m "<message>"`, and `git push`.
- Refuses to run on `main` (print: "Create a feature branch first") — use `git branch --show-current`.

### Git hook: `pre-commit`
Create `.git/hooks/pre-commit` that:
- Blocks the commit if any staged `.sh` file is not executable or does not start with `#!/bin/bash`.
- Prints which file failed.

Since `.git/hooks` is not tracked, also save a copy as `hooks/pre-commit` in the repo and add a line in `README.md` explaining how to install it.

## Part 8: Documentation (not graded separately, required)

Your `README.md` must include:
- A table listing every script with a one-line description and usage example.
- Instructions to clone and run the scripts.
- A screenshot or pasted output of at least 2 scripts running.

---

## Submission

Submit your **GitHub repo link** (public). It must contain:

- [ ] At least **15 meaningful commits**
- [ ] At least **1 merged Pull Request**
- [ ] The **conflict resolution commit** visible in history
- [ ] All 7 scripts in `scripts/`, executable
- [ ] `docs/graph.png`
- [ ] `hooks/pre-commit`
- [ ] Complete `README.md`

## Grading

| Item | Points |
|---|---|
| Local Git basics and `.gitignore` | 10 |
| Scripts 1-5 (working, clean, error handling) | 25 |
| Branching and merging | 10 |
| GitHub remote + Pull Request | 15 |
| Conflict resolution | 10 |
| Undo section | 10 |
| Scripts 6-7 and pre-commit hook | 20 |
| **Total** | **100** |

Deductions: **-5** for vague commit messages like "update" or "fix"; **-5** for scripts missing the shebang or error handling; **-10** for copied work.

## Bonus (+15)

- **+5:** Run `shellcheck` on all scripts and fix every warning. Add a note in the README.
- **+5:** Add a GitHub Actions workflow `.github/workflows/lint.yml` that runs `shellcheck` on every push.
- **+5:** Write `cleanup.sh` that deletes all local branches already merged into `main` (except `main` itself).

---

**Tips**
- Commit small and often. Write messages like `Add backup retention logic`, not `changes`.
- Test scripts with bad input (missing arguments, non-existent folders) before committing.
- Use `echo $?` to check exit codes.
- Stuck? Run `man <command>` or `<command> --help` first.
