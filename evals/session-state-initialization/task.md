# Initialize Code Review Tracking State

## Problem/Feature Description

Your team uses Codex CLI for automated code review. A colleague ran a review session earlier today and captured the full terminal output (JSONL stream) along with the review report file. Before they left for the day, they asked you to pick up where they left off and set up the tracking infrastructure so the review can continue in a follow-up session.

The raw output from the Codex run is provided below. You need to parse it, extract the relevant session information, and initialize the review workspace with the correct directory structure and state file so the review process can be resumed later.

## Output Specification

Set up the following in the current working directory:
- A directory for storing code review artifacts
- A state tracking file named `state.json` within that directory, populated with the session information and any findings identified from the review report

The review report (saved separately from the JSONL stream) is also provided below.

## Input Files

The following files are provided as inputs. Extract them before beginning.

=============== FILE: inputs/codex-stdout.jsonl ===============
{"type":"message","role":"assistant","content":"Starting repository analysis..."}
{"type":"message","role":"assistant","content":"Scanning Go source files..."}
{"type":"progress","percent":25,"message":"Analyzing main package"}
{"type":"progress","percent":50,"message":"Analyzing handlers package"}
{"type":"session_meta","session_id":"f7e2a1b9c34d8e5f","model":"codex-1","started_at":"2024-03-15T14:22:01Z","flags":["json","review"]}
{"type":"progress","percent":75,"message":"Analyzing utils package"}
{"type":"progress","percent":100,"message":"Analysis complete"}
{"type":"message","role":"assistant","content":"Review complete. Found 3 issues. Report saved to review-output.md"}

=============== FILE: inputs/review-output.md ===============
# Code Review Report

## Summary

Reviewed diff against `main` branch. Found 3 issues across 2 files.

---

### Finding 1 — [P-high]

**File:** `cmd/server.go:112`

**Issue:** The HTTP server is started without a timeout configuration. Long-running connections can exhaust file descriptors under load.

**Suggestion:** Set `ReadTimeout`, `WriteTimeout`, and `IdleTimeout` on the `http.Server` struct before calling `ListenAndServe`.

---

### Finding 2 — [P-medium]

**File:** `internal/db/queries.go:47`

**Issue:** User-supplied input is interpolated directly into the SQL query string using `fmt.Sprintf`. This is a textbook SQL injection vulnerability.

**Suggestion:** Replace `fmt.Sprintf` with parameterized queries using `db.QueryContext` with placeholder arguments.

---

### Finding 3 — [P-low]

**File:** `cmd/server.go:89`

**Issue:** The `logger` variable is declared but its value is immediately overwritten on the next line without the initial value being used. This is a dead assignment.

**Suggestion:** Either remove the initial assignment or restructure the code so the initial value is actually used.
