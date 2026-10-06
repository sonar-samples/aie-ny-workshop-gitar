# Gitar workshop: Boxoffice

Boxoffice is the ticketing API behind a small venue's website. Customers buy tickets, each ticket carries a QR code, and gate staff scan it at the door. In this workshop you open three pull requests in your own fork and watch [Gitar](https://gitar.ai), Sonar's AI code verification agent, review them, fix them, and merge one. Findings and fixes can differ a little between runs.

| Stage | What you'll see | Branch |
|---|---|---|
| 1 | An out-of-the-box review, then a fix from one comment | `stage-1` |
| 2 | How a short `.gitar/` instruction and rule change a review | `stage-2` |
| 3 | A failing CI run, diagnosed, fixed, and merged from one comment | `stage-3` |

## Before you start

You need a GitHub account where you can install a GitHub App. Sign in at [app.gitar.ai](https://app.gitar.ai) first and start the GitHub install from there. Installing from GitHub Marketplace while signed out leaves nothing connected. Your 14-day Pro trial starts when you connect GitHub.

## Set up your fork

Complete setup before opening the three pull requests. If your pull requests are open, finish setup now and continue with the stages; you don't need to recreate them.

1. Fork this repo. **Uncheck "Copy the `main` branch only"**, then confirm your fork shows 4 branches.
2. In your fork, open the **Actions** tab and click **I understand my workflows, go ahead and enable them**.

## Configure GitHub merge controls

Finish these settings before posting the Stage 3 commands if you want it to merge automatically. Stages 1 and 2 work without them. If you skip this section, Stage 3 ends with a fix commit and green CI, and the presenter shows the merge.

1. In your fork, open **Settings > General > Pull Requests** and check **Allow auto-merge**.
2. Open **Settings > Rules > Rulesets > New ruleset > New branch ruleset** and fill it in like this. Leave every field not listed at its default.

   | Field | Value |
   |---|---|
   | Ruleset name | `require-tests` |
   | Enforcement status | **Active** |
   | Bypass list | Empty |
   | Target branches | **Add target > Include default branch** |
   | Restrict deletions | Checked (default) |
   | Block force pushes | Checked (default) |
   | Require a pull request before merging | Unchecked |
   | Require status checks to pass | Checked |
   | Require branches to be up to date before merging | Unchecked |
   | Status checks | **Add checks**, type `test`, select **test** (GitHub Actions) |

   Then click **Create**. If `test` doesn't show up in the search, run **Actions > Test > Run workflow** on `main` and search again. Instead of filling in the form, you can use **New ruleset > Import a ruleset** and upload `setup/ruleset-require-tests.json` from this repo.

## Connect and configure Gitar

1. In Gitar, open **Settings > Configuration**, grant Gitar access to your fork, and confirm the fork is listed.
2. In the behavior settings for your fork, turn on **Auto-approve PRs based on code review** and leave **Auto-merge PRs after auto-approve** off. If a merge method is selectable, choose **Squash**.

GitHub's **Allow auto-merge** setting permits automatic merging, while Gitar's auto-approve setting lets it submit an approval after its review. Leave Gitar's auto-merge setting off so Stages 1 and 2 stay open. Later, `gitar auto-merge:on` enables merging for the Stage 3 pull request only, and GitHub waits for the required `test` check. The command still needs Gitar's auto-approve setting on.

| Where | Setting | Value |
|---|---|---|
| GitHub, your fork | Allow auto-merge | On for the full Stage 3 cycle |
| Gitar, your fork's behavior settings | Auto-approve PRs based on code review | On |
| Gitar, your fork's behavior settings | Auto-merge PRs after auto-approve | Off |

## Open the three pull requests

Create each pull request through GitHub's form in your fork.

1. Open **Pull requests > New pull request**.
2. Select your fork for both **base repository** and **head repository**. Set **base** to `main` and **compare** to `stage-3`.
3. Click **Create pull request**. The branch's commit fills in the title and description; keep both and submit the pull request.
4. Repeat with **compare** set to `stage-1`, then `stage-2`, keeping your fork as both repositories and `main` as the base.

Stage 3 goes first because its CI analysis takes longest. The command that enables merging for that pull request comes later, after you read its diagnosis. Leave the Stage 1 and Stage 2 pull requests open throughout the workshop.

## Stage 1: out-of-the-box review, then `gitar fix`

Read the diff while Gitar reviews it, which can take several minutes. Find Gitar's inline finding and reply `gitar fix` in its thread. When Gitar's commit lands, open it, look at which files it changed, and check that CI is green. Don't merge this pull request.

## Stage 2: context ingestion

Before you read the review, open the pull request's **Files changed** tab and read the two files under `.gitar/`. Then read Gitar's finding, the comment the rule posted, and the **Rules** section of Gitar's dashboard comment.

An instruction in `.gitar/review/` changes what the review looks for, while a rule in `.gitar/rules/` makes Gitar take an action when a pull request event matches. This stage is review only, so don't fix or merge it.

## Stage 3: CI failure, then auto-apply and auto-merge

The pull request's `test` check fails. Read Gitar's CI analysis in its dashboard comment first, because applying the fix replaces it. Then post one comment.

If you set up merge controls, comment both commands, one per line. Done means the pull request is merged.

```text
gitar auto-apply:on
gitar auto-merge:on
```

If you skipped merge controls, comment only `gitar auto-apply:on`. Done means a Gitar commit and green CI.

Either way, check that Gitar's commit changed application code, not a test. With merge controls configured, Gitar approves the code and GitHub waits for the required `test` check to turn green before merging.

## Troubleshooting

| Problem | What to do |
|---|---|
| Review is taking longer than expected | Check Gitar's dashboard. If a review is still running, wait for it to finish. If no review is running, comment `gitar review` |
| Pull request opened against `sonar-samples` | Close it, then use New pull request and select your fork as both base repository and head repository |
| No `test` check on a pull request | Enable Actions in your fork, then close and reopen the pull request |
| Stage 3 shows no CI analysis | Comment `gitar review` |
| Gitar's dashboard says auto-merge wasn't armed | Check GitHub's Allow auto-merge and required `test` check, Gitar's auto-approve setting, and the Stage 3 `gitar auto-merge:on` command |

## Take it further

- Stage 3 changes your `main` when it merges, so to re-run the workshop, delete your fork (**Settings > Danger Zone**) and fork again with "Copy the `main` branch only" unchecked.
- To make the Stage 2 context permanent, copy the `.gitar/` folder from the `stage-2` branch to `main` in your fork. The instruction and rule then apply to every pull request. Add a rule of your own beside them.
- Gitar reads your coding agent's instruction files: `AGENTS.md`, `CLAUDE.md`, `.cursorrules`, and files one level deep in `.cursor/rules/` and `.claude/rules/`. Add an `AGENTS.md` with one team rule, open a pull request, and watch the review apply it. Keep instruction files short, because Gitar's instruction budget is 8k tokens.
- Add a skill at `.gitar/skills/<name>/SKILL.md` with `name` and `description` frontmatter, then trigger it in plain language: `gitar run the <name> skill on this`. Slash syntax does nothing. Gitar also reads skills from `.claude/skills/`, `.agents/skills/`, and `.github/skills/`.
- Write plain-language approval criteria in `.gitar/config/approve.md` and merge criteria in `.gitar/config/merge.md`. Gitar reads `merge.md` from the default branch only, so a pull request can't grant itself auto-merge.
- On the Pro plan, connect Slack or Jira under **Settings > Integrations**, then name it in a rule, for example "post a summary to #releases."
- Other useful commands are `gitar review`, `gitar display:verbose`, and `gitar auto-apply:off`.
- Enterprise adds features including knowledge with citations, cross-repo and cross-PR analysis, and functional validation against linked issues.
