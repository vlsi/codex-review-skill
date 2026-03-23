# Code Review Report Generator

## Problem/Feature Description

An engineering manager at a consulting firm needs a consistent, structured report after every automated code review session. The firm delivers these reports to clients as part of their code audit service, so the format must be professional and predictable — clients need to be able to scan quickly for what was found, what was changed, and what was deliberately left alone.

The manager has a completed review session: two iterations were run against a TypeScript utility library, findings were classified, some were fixed and some were skipped. Now they need a properly formatted final report and need the temporary working files cleaned up so they don't accumulate on the build server.

Your job is to generate the final report from the session data provided, then clean up the temporary directory.

## Input Files

The following files are provided as inputs. Extract them before beginning.

=============== FILE: inputs/session_data.json ===============
{
  "session_id": "sess-ts-audit-007",
  "iteration": 2,
  "findings": [
    {
      "id": 1,
      "file": "src/utils/date_helper.ts",
      "lines": "23-24",
      "description": "Date comparison uses '==' instead of strict '==='. In TypeScript with strict mode disabled this can cause unexpected type coercion.",
      "severity": "P3-medium",
      "status": "fixed",
      "resolution": "Changed '==' to '===' on line 23 and 24"
    },
    {
      "id": 2,
      "file": "src/utils/string_helper.ts",
      "lines": "45",
      "description": "Function `trimAndLower` does not handle null input — will throw a TypeError if called with null.",
      "severity": "P2-high",
      "status": "fixed",
      "resolution": "Added null guard: if (!input) return '' at the start of the function"
    },
    {
      "id": 3,
      "file": "src/utils/array_helper.ts",
      "lines": "12",
      "description": "Array method `.forEach` used where `.map` would be more idiomatic for the transformation pattern.",
      "severity": "P4-low",
      "status": "skipped",
      "resolution": "Team preference — existing codebase consistently uses forEach for side effects"
    },
    {
      "id": 4,
      "file": "src/utils/date_helper.ts",
      "lines": "23",
      "description": "After fix in iteration 1, equality check is now strict. No further issues found in date_helper.",
      "severity": "P4-low",
      "status": "skipped",
      "resolution": "Previously fixed — no remaining issues"
    }
  ],
  "fixed": [1, 2],
  "skipped": [3, 4]
}
=============== END FILE ===============

=============== FILE: inputs/temp_dir_path.txt ===============
/tmp/codex-review-Xk7m2p
=============== END FILE ===============

## Output Specification

Using the session data above, produce:

1. **`review_report.md`** — A final review report. The format and structure of this report should be professional and follow a logical organization for communicating audit results to a client. Include all findings, their outcomes, and a final verdict on whether the codebase is clean.

2. **`cleanup.sh`** — A shell script that removes the temporary working directory referenced in `inputs/temp_dir_path.txt`. The script should read the path from the file and remove the directory safely.
