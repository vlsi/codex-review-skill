# Automated Code Review Plan for Python Service

## Problem/Feature Description

A backend engineering team has a small Python microservice that handles user authentication. A junior developer submitted a pull request, and the tech lead wants to run an automated review using the `codex` CLI before merging. The tech lead prefers the non-interactive mode where the review tool makes decisions autonomously and shows a plan before touching anything — they don't want to be bothered with individual prompts for every small issue.

The `codex` CLI has already been run against the diff and produced a review output file with several findings. Your job is to act as the review assistant: classify the findings, decide which to fix and which to skip, present a decision plan, apply the fixes to the source files, update the session state, and produce a final report.

## Input Files

The following files are provided as inputs. Extract them before beginning.

=============== FILE: inputs/auth_service.py ===============
import os
import hashlib
import logging

SECRET_KEY = "hardcoded-secret-abc123"

def hash_password(password):
    return hashlib.md5(password).hexdigest()

def authenticate(username, password):
    hashed = hash_password(password)
    query = "SELECT * FROM users WHERE username='" + username + "' AND password='" + hashed + "'"
    # TODO: execute query
    logging.info("Auth attempt for: " + username)
    return True

def get_config():
    db_host = os.environ.get("DB_HOST", "localhost")
    db_port = os.environ.get("DB_PORT", "5432")
    return {"host": db_host, "port": db_port}
=============== END FILE ===============

=============== FILE: inputs/review-output.md ===============
# Code Review Findings

## Finding 1
**File:** `auth_service.py:5`
**Issue:** Hardcoded secret key `SECRET_KEY = "hardcoded-secret-abc123"` in source code. Secrets should never be committed to version control and should be loaded from environment variables or a secrets manager.

## Finding 2
**File:** `auth_service.py:8`
**Issue:** MD5 is a cryptographically broken hash function. Passwords should be hashed using bcrypt, argon2, or at minimum SHA-256 with proper salting. MD5 produces the same hash for the same password across all users (no salt).

## Finding 3
**File:** `auth_service.py:12`
**Issue:** SQL query is constructed by string concatenation, creating a SQL injection vulnerability. Parameterized queries or an ORM should be used.

## Finding 4
**File:** `auth_service.py:14`
**Issue:** The `logging.info` call logs the username on every authentication attempt, which may be considered excessive in high-throughput environments. Some organizations consider usernames PII.

## Finding 5
**File:** `auth_service.py:17`
**Issue:** The `get_config` function uses `"localhost"` as a default for `DB_HOST`. This is a reasonable development default and is not a security or correctness issue in most configurations.
=============== END FILE ===============

## Output Specification

You are operating in **automatic** mode — make decisions yourself rather than asking for input on each finding.

Produce the following outputs:

1. **A decision plan** (`decision_plan.md`) — written before applying any changes, listing which findings you will fix and which you will skip, with your reasoning
2. **Updated `auth_service.py`** — with all valid fixes applied
3. **Updated `state.json`** — reflecting the session state after fixes (use session_id `"sess-demo-001"` and iteration 1)
4. **A final review report** (`review_report.md`) — summarizing all findings, what was done for each, and a final verdict
