# Multi-Pass Code Review Script for a Node.js API

## Problem/Feature Description

A DevOps engineer at a SaaS company is building an automated code quality gate for their Node.js REST API. They want a shell script that runs a full multi-pass code review using the `codex` CLI: an initial review pass, then one or two follow-up passes that give the review tool context about what was already fixed — so the follow-up doesn't re-flag issues that were deliberately addressed or intentionally left alone.

The engineer tried simply running the same review command twice and found the tool kept raising the same issues without any awareness of previous fixes. They need the follow-up passes to resume an existing session rather than starting fresh, and they need the loop to terminate sensibly — either when there's nothing left to fix or after a bounded number of passes to prevent infinite loops.

Write a shell script that orchestrates this multi-pass review loop. The script should be documented with comments explaining key decisions (especially around the resume mechanism and termination logic).

## Input Files

The following files are provided as inputs. Extract them before beginning.

=============== FILE: inputs/api_server.js ===============
const express = require('express');
const app = express();

const DB_PASSWORD = 'prod-password-123';

app.use(express.json());

app.get('/users/:id', (req, res) => {
  const userId = req.params.id;
  const query = `SELECT * FROM users WHERE id = ${userId}`;
  // db.query(query) - not implemented
  res.json({ id: userId, query: query });
});

app.post('/login', (req, res) => {
  const { username, password } = req.body;
  if (password === DB_PASSWORD) {
    res.json({ token: 'fake-jwt-token' });
  } else {
    res.status(401).json({ error: 'Unauthorized' });
  }
});

app.listen(3000);
=============== END FILE ===============

=============== FILE: inputs/iteration1_review.md ===============
# Iteration 1 Review Findings

## Finding 1
**File:** `api_server.js:4`
**Issue:** Hardcoded `DB_PASSWORD` in source. Passwords must be loaded from environment variables.

## Finding 2
**File:** `api_server.js:10`
**Issue:** SQL query built with string interpolation — SQL injection risk. Use parameterized queries.

## Finding 3
**File:** `api_server.js:18`
**Issue:** Password compared in plaintext against hardcoded value. Authentication should validate against a hashed password from a database.
=============== END FILE ===============

=============== FILE: inputs/iteration2_review.md ===============
# Iteration 2 Review Findings

## Finding 4
**File:** `api_server.js:4`
**Issue:** `process.env.DB_PASSWORD` is used but there is no fallback or startup validation — the app will silently use `undefined` if the variable is not set.
=============== END FILE ===============

## Output Specification

Produce the following outputs:

1. **`review_loop.sh`** — A shell script that:
   - Creates a working directory for review artifacts
   - Runs an initial `codex exec review` command for the `--uncommitted` target
   - Parses the session ID from the command output
   - Saves initial state to a state file
   - After fixes are applied, runs a follow-up review pass using the appropriate resume mechanism with a message that contextualizes what was fixed and what was skipped
   - Implements a loop that terminates under the right conditions
   - Cleans up the working directory when done

   Use comments to explain the resume mechanism and termination logic.

2. **`api_server.js`** — Fixed version with findings 1, 2, and 3 addressed (finding 4 is new and appears in iteration 2)

3. **`state_after_iteration1.json`** — State file contents after iteration 1 completes (use session_id `"sess-nodejs-001"`)

4. **`state_after_iteration2.json`** — State file contents after iteration 2 completes
