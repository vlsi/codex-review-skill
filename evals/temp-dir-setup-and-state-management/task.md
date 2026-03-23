# Code Review Automation Script

## Problem/Feature Description

A platform team at a mid-size software company wants to standardize how developers run automated code reviews in CI pipelines and developer scripts. They need a reusable shell script that wraps the `codex` CLI to perform a review of committed changes, captures the review output, and records session state so the review process can be resumed or audited later.

The script should invoke `codex` to review a specific git commit, capture its output to a dedicated intermediate file, extract the session identifier from the tool's output, and write a state file that tracks progress through the review workflow. The platform team wants the script to be safe to run inside any project repository without leaving any artifacts inside the project itself.

## Output Specification

Write a shell script named `codex_review_setup.sh` that:
1. Accepts a commit SHA as a command-line argument (e.g., `./codex_review_setup.sh abc1234`)
2. Creates a working directory for review artifacts
3. Runs `codex` to review the specified commit, capturing its output appropriately
4. Parses the `codex` output to extract the session identifier
5. Writes a state file recording the session id, current iteration, and empty tracking lists for findings, fixed items, and skipped items

The script should print the path of the working directory it created to stdout so callers can reference it.

Also write a short `README_setup.md` explaining:
- Where the working directory is created and why that location was chosen
- How the session ID is extracted from the codex output format
- The structure of the state file written
