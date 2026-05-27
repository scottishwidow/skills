# Issue tracker: Jira

Issues and PRDs for this repo live as Jira issues. Use the [`acli`](https://developer.atlassian.com/cloud/acli/) (Atlassian CLI) for all operations — specifically the `acli jira` subcommand.

The CLI must be authenticated first with `acli jira auth login` — that records the Jira site and credentials. Skills assume this has already been run; if `acli jira auth status` reports no active session, prompt the user to log in.

## Formatting: plain text only

Jira does **not** render Markdown. It uses its own wiki/ADF markup, which is fiddly to author and inconsistent across sites. When a skill writes an issue summary, description, or comment, write **plain text** — no `#` headings, no `**bold**`, no fenced code blocks, no Markdown links.

Acceptable substitutes:

- Headings → ALL-CAPS line followed by a blank line, or a line ending in `:`
- Bullets → `- ` is fine (Jira renders it acceptably as a literal dash)
- Code / commands → indent with two spaces, or wrap in `{{...}}` only if the user has explicitly opted into Jira wiki markup
- Links → write the bare URL on its own line; Jira auto-linkifies

If the user has explicitly told you their Jira site renders Markdown (some plugins enable this), follow their instruction. Default is plain text.

## Conventions

- **Create an issue**: `acli jira workitem create --type "Task" --summary "..." --description "..."`. Use a heredoc for multi-line descriptions, or `--description-file <path>` to read from a file. Pass `--project <KEY>` if the default project isn't right.
- **Read an issue**: `acli jira workitem view <KEY-123>`. Add `--json` for machine-readable output.
- **List issues**: `acli jira workitem search --jql "project = PROJ AND status = 'To Do'" --json`. JQL is the filter language; use it for label, status, and assignee filters.
- **Comment on an issue**: `acli jira workitem comment create --key <KEY-123> --body "..."`. Use `--body-file <path>` for multi-line.
- **Apply / remove labels**: `acli jira workitem update --key <KEY-123> --label "..."` (repeatable). To remove, refetch the current labels via `acli jira workitem view --json`, drop the unwanted ones, and re-set with `--label` flags. Jira label semantics differ between sites — confirm by reading the issue back.
- **Transition state**: Jira uses workflow transitions, not state strings. `acli jira workitem transition --key <KEY-123> --name "Done"` (substitute the project's transition name). Run `acli jira workitem transition --key <KEY-123> --list` once to discover the available transitions for this project.
- **Close**: There is no universal "close" — use the project's terminal transition (often `Done`, `Closed`, or `Won't Do`). Post the closing rationale with `acli jira workitem comment create` first, then transition.

Issue keys look like `PROJ-123`, not bare numbers. Always pass the full key.

## When a skill says "publish to the issue tracker"

Create a Jira issue with `acli jira workitem create`. Write the description in plain text (see formatting note above).

## When a skill says "fetch the relevant ticket"

Run `acli jira workitem view <KEY>`.
