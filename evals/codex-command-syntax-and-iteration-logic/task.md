# Automate the Codex Code Review Loop

## Problem/Feature Description

The platform team wants to standardize how developers run Codex-based code reviews across dozens of repositories. Rather than each developer manually running commands and tracking state, the team wants a reusable bash script that encodes the full review loop: run the initial review, capture the session state, apply fixes, then resume the session for re-review — continuing until the review is clean or a maximum number of attempts has been reached.

A senior engineer has outlined the requirements: the script should accept the same arguments that get passed through to the Codex review command, capture the JSONL output to extract session information, persist state between iterations, and include the context about what was fixed and skipped when resuming the session. The script should stop automatically when there's nothing left to fix or when it has iterated enough times.

## Output Specification

Write a bash script named `codex_review_loop.sh` that implements the automated review loop described above. The script should:
- Accept command-line arguments that are forwarded to the review command
- Handle the full multi-iteration flow including initial review and follow-up sessions
- Save and load state between iterations
- Stop at the appropriate conditions

Also write a brief `README.md` explaining how to use the script and what arguments it accepts.
