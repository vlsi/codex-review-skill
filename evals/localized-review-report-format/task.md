# Generate Code Review Summary Report

## Problem/Feature Description

A backend engineering team uses an automated code review pipeline based on Codex CLI. After two iterations of review and fixes, the process has completed. The team lead needs a final human-readable report that documents every finding, what was done about it, and the overall verdict. This report will be attached to the pull request and reviewed by the team.

The completed review session state is provided below. Generate the final report based on this data. The team lead communicates in Spanish and expects the report in their language. Technical terms such as file paths, function names, and error types should remain in their original form.

## Output Specification

Write the completed review report to a file named `review_report.md`.

## Input Files

The following files are provided as inputs. Extract them before beginning.

=============== FILE: inputs/state.json ===============
{
  "session_id": "f7e2a1b9c34d8e5f",
  "iteration": 2,
  "findings": [
    {
      "id": 1,
      "severity": "P-high",
      "file": "cmd/server.go:112",
      "issue": "HTTP server started without timeout configuration. Long-running connections can exhaust file descriptors under load.",
      "decision": "fixed",
      "change": "Added ReadTimeout, WriteTimeout, and IdleTimeout fields to the http.Server struct initialization. Was: `&http.Server{Addr: addr, Handler: mux}`, now: `&http.Server{Addr: addr, Handler: mux, ReadTimeout: 30*time.Second, WriteTimeout: 30*time.Second, IdleTimeout: 60*time.Second}`"
    },
    {
      "id": 2,
      "severity": "P-medium",
      "file": "internal/db/queries.go:47",
      "issue": "User-supplied input interpolated directly into SQL query string using fmt.Sprintf. Classic SQL injection vulnerability.",
      "decision": "fixed",
      "change": "Replaced fmt.Sprintf with parameterized query using db.QueryContext with placeholder arguments. Was: `query := fmt.Sprintf(\"SELECT * FROM users WHERE id = %s\", userID)`, now uses `db.QueryContext(ctx, \"SELECT * FROM users WHERE id = $1\", userID)`"
    },
    {
      "id": 3,
      "severity": "P-low",
      "file": "cmd/server.go:89",
      "issue": "The logger variable is declared but its value is immediately overwritten on the next line without the initial value being used. Dead assignment.",
      "decision": "skipped",
      "reason": "False positive — analysis of the surrounding code shows the initial logger value IS used in a defer statement on line 91 that was not included in the reviewed diff."
    },
    {
      "id": 4,
      "severity": "P-low",
      "file": "internal/cache/lru.go:23",
      "issue": "Second iteration: mutex lock is acquired but there is a code path (early return on line 31) that exits without releasing the lock, causing a potential deadlock.",
      "decision": "fixed",
      "change": "Moved the mutex unlock to a defer statement immediately after the lock acquisition. Was: manual Unlock() calls at each return point, now: `defer c.mu.Unlock()` on line 24."
    }
  ],
  "fixed": [1, 2, 4],
  "skipped": [3]
}
