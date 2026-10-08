# Assignment: GitHub Organization, Pull Requests, Rulesets & Merge Commits

**Format:** Teams of 3 | **Time:** 3-4 hours | **Total:** 100 points (+10 bonus)

**Goal:** Practice real team workflow on GitHub: organization, repo, protected `main`, PRs with approval, and handling two branches that diverge from the same commit.

## Roles (rotate if you want to repeat the exercise)

| Role | Branch | Job |
|---|---|---|
| **Owner** | `main` | Creates the org and repo, sets the ruleset, reviews PRs |
| **Chirag** (Dev 1) | `chirag` | Works on a feature, merges first |
| **Krishna** (Dev 2) | `krishna` | Works on a feature, merges second, must catch up with `main` |

Use real teammate names if you like. The branch names stay as shown.

## The scenario you will recreate

```
main:     A --- B --- C ------------ D ------------- H   (merge commit)
                       \           /               /
chirag:                 \-- C' -- D'              /
                         \                       /
krishna:                  \-- C'' - E - F - G --/
                                         (after merging main into krishna)
```

1. Both developers start from the same commit `C` on `main`.
2. Chirag finishes first. His PR is approved and merged. `main` moves forward.
3. Krishna's branch is now **behind** `main`. He must bring `main` into his branch, fix any conflicts, push, and open his PR.
4. Krishna's PR is merged with a **merge commit** (`H`).

---

## Stage 1: Organization & Repository (15 pts)

1. **Owner:** create a free GitHub Organization named `<teamname>-devops`.
2. Invite Chirag and Krishna as members.
3. Create a new repo `team-project` inside the org (public, with a README, `.gitignore` for your language of choice).
4. Give both teammates **Write** access to the repo (Settings > Collaborators and teams).
5. All three clone the repo locally using SSH.
6. Owner makes the first commits on `main`: `A` (add `app.sh`), `B` (add `docs/notes.md`), `C` (add `config.txt`). One commit each, clear messages.

**Proof:** screenshot of the org members page and the repo commit list.

## Stage 2: Branch Ruleset (15 pts)

**Owner:** go to repo Settings > Rules > Rulesets > New branch ruleset.

Create a ruleset named `protect-main` with:
- Enforcement: **Active**
- Target: default branch (`main`)
- **Restrict deletions**
- **Require a pull request before merging**
  - Required approvals: **1**
  - Dismiss stale approvals when new commits are pushed
- **Block force pushes**
- Allowed merge methods: **Merge commit** only (disable squash and rebase for this assignment)

**Test it:** each teammate tries on their own machine:
```bash
git checkout main
echo "test" >> README.md
git commit -am "direct push test"
git push origin main
```
This should be **rejected**. Paste the error message in your submission, then undo the local commit (`git reset --hard origin/main`).

**Answer in `answers.md`:** why do teams block direct pushes to `main`?

## Stage 3: First PR, Approve, Merge (15 pts)

Do one simple full cycle with Chirag as the author.

1. Chirag: `git checkout -b chirag`, add `chirag-feature.sh`, commit, `git push -u origin chirag`.
2. Chirag opens a PR `chirag` -> `main` with a title, description and a reviewer assigned.
3. Owner reviews the **Files changed** tab, leaves one inline comment, then **approves**.
4. Notice what happens if Chirag tries to approve his own PR (it should not count). Screenshot it.
5. Merge using **Merge commit**.
6. Everyone runs:
   ```bash
   git checkout main
   git pull origin main
   git log --oneline --graph --all
   ```

**Proof:** PR link + graph screenshot.

> Reset for Stage 4: for this stage, treat the state **before** Chirag's merge as the starting point. If you already merged, create a fresh commit `C` on `main` and start Stage 4 from there with new branches.

## Stage 4: Two Branches from the Same Commit (20 pts)

Now recreate the diagram.

1. Make sure `main` is at commit `C`. Both Chirag and Krishna run:
   ```bash
   git checkout main
   git pull origin main
   ```
2. **Chirag:** `git checkout -b chirag-2`
   - Edit `config.txt` (change line 1 to `owner=chirag`) and commit as `C'`.
   - Add `chirag-2.sh` and commit as `D'`.
   - Push the branch.
3. **Krishna:** `git checkout -b krishna`
   - Edit the **same line 1** of `config.txt` (`owner=krishna`) and commit.
   - Add three more commits `E`, `F`, `G` (new files or edits elsewhere).
   - Push the branch.
4. Open **two PRs** into `main`, one for each branch.
5. Owner approves and merges **Chirag's PR first** using a merge commit.
6. Look at Krishna's PR on GitHub. Screenshot the message about the conflict or "branch is out-of-date".

**Answer in `answers.md`:** why is Krishna's branch now "behind", and why did the conflict appear only after Chirag merged?

## Stage 5: Catch Up and Merge Commit (20 pts)

**Krishna** brings the latest `main` into his branch. Use this exact flow:

```bash
git checkout krishna
git fetch origin
git merge origin/main        # conflict in config.txt expected
```

1. Open `config.txt`, resolve the conflict markers (keep both values, for example `owner=chirag,krishna`).
2. Finish the merge:
   ```bash
   git add config.txt
   git commit                # keep the default merge message
   git push origin krishna
   ```
3. Krishna's PR updates automatically and shows no conflicts. Owner approves it.
4. Merge the PR with **Merge commit**. This creates `H` on `main`.
5. Everyone syncs:
   ```bash
   git checkout main
   git pull origin main
   git log --oneline --graph --all
   ```

**Proof:** the final graph screenshot. You should clearly see the merge commit `H` with two parents.

Run this and paste the output in `answers.md`:
```bash
git show --no-patch --format="%h %p %s" HEAD
```
The `%p` field should list **two** parent hashes.

## Stage 6: Understanding Check (15 pts)

Answer these in `answers.md`, in your own words (2-4 lines each). Do not copy from the internet.

1. What is the difference between `git fetch`, `git pull` and `git merge origin/main`?
2. What does `origin/main` mean? How is it different from local `main`?
3. What is a merge commit and why does it have two parents?
4. In the scenario, why must Krishna merge `main` into his branch **before** his PR can be merged cleanly?
5. What problem does a branch ruleset solve? Name two rules you enabled.
6. Draw (ASCII or hand-drawn photo) the final commit graph with `A, B, C, C', D', E, F, G, H` labeled.

---

## Submission

Create a file `submission.md` in the **repo root** (via a PR, not a direct push!) containing:

- [ ] Org name and repo link
- [ ] Links to all PRs (at least 3 merged)
- [ ] Screenshots: org members, ruleset settings, rejected push, self-approve block, conflict message, final graph
- [ ] `answers.md` linked

## Grading

| Stage | Points |
|---|---|
| 1. Org and repo setup | 15 |
| 2. Ruleset configured and tested | 15 |
| 3. PR, review, approval, merge | 15 |
| 4. Diverged branches and two PRs | 20 |
| 5. Conflict resolution and merge commit | 20 |
| 6. Understanding questions | 15 |
| **Total** | **100** |

**Bonus (+10):**
- **+5:** Add a `CODEOWNERS` file so the Owner is auto-requested on every PR.
- **+5:** Create a second ruleset that requires PR titles to match a pattern (for example start with `feat:` or `fix:`), then show a rejected and an accepted case.

## Common mistakes

- Pushing straight to `main` (the ruleset should stop you; if it does not, your ruleset is not active).
- Forgetting `git fetch` before `git merge origin/main`, so you merge old data.
- Resolving the conflict but forgetting `git add` before `git commit`.
- Deleting the branch before the PR is merged.
- Approving your own PR and wondering why it will not merge.