# Design a Step-by-Step Code Review Workflow

## Problem/Feature Description

A security-conscious fintech startup wants a code review workflow where a developer personally approves or overrides every suggested change before it is applied. They've had issues in the past with automated tools making changes without developer awareness, leading to regressions. The team lead wants a design document and prototype workflow that walks through their Go payment handler code one finding at a time, letting the reviewer decide the fate of each issue individually.

You have access to the `codex` CLI which has already reviewed the payment handler and produced findings. Your task is to design and document the step-by-step interactive workflow, then simulate running it against the provided findings — showing exactly what prompts would be presented to the user, what the three choices are for each finding, and how the resulting decisions would be recorded.

## Input Files

The following files are provided as inputs. Extract them before beginning.

=============== FILE: inputs/payment_handler.go ===============
package payment

import (
	"fmt"
	"log"
	"net/http"
)

var apiKey = "sk_live_abc123xyz789"

func ProcessPayment(w http.ResponseWriter, r *http.Request) {
	amount := r.FormValue("amount")
	cardNumber := r.FormValue("card_number")

	log.Printf("Processing payment for card: %s amount: %s", cardNumber, amount)

	if amount == "" {
		fmt.Fprintf(w, "error: missing amount")
		return
	}

	// Call payment processor
	result := chargeCard(cardNumber, amount)
	fmt.Fprintf(w, "result: %s", result)
}

func chargeCard(card string, amount string) string {
	// TODO: implement
	return "ok"
}
=============== END FILE ===============

=============== FILE: inputs/review-output.md ===============
# Code Review Findings

## Finding 1
**File:** `payment_handler.go:10`
**Issue:** Hardcoded API key `apiKey = "sk_live_abc123xyz789"` is present in source code. Live API keys must never be committed — use environment variables or a secrets vault.

## Finding 2
**File:** `payment_handler.go:16`
**Issue:** Full card number is logged via `log.Printf`. Logging PAN (Primary Account Number) data violates PCI-DSS compliance requirements. Card numbers must be masked before logging (e.g., show only last 4 digits).

## Finding 3
**File:** `payment_handler.go:19-21`
**Issue:** Only `amount` is validated for emptiness but `card_number` has no validation. An empty or malformed card number will be passed to `chargeCard` without any error handling.

## Finding 4
**File:** `payment_handler.go:11-12`
**Issue:** Unused import `"net/http"` is referenced only for handler types. This is standard Go — no action needed, this is how HTTP handlers are structured in Go's stdlib.
=============== END FILE ===============

## Output Specification

Produce the following outputs:

1. **`workflow_design.md`** — A document describing the interactive review workflow: how mode selection works, the exact options presented to the user for each finding, and how state is tracked. Include the mode-selection prompt the user would see at the start.

2. **`workflow_simulation.md`** — A simulated run of the interactive workflow against the four findings above. For each finding show: the prompt that would be presented to the user (with all available choices), a chosen response, and any action taken. Also show any prompts presented at the end of the iteration. Assume the user decides: address finding 1, skip finding 4 because it's not actually a problem, and handle finding 3 with a custom approach (return a masked placeholder for empty card numbers rather than an error).

3. **`state.json`** — The final state file after the simulation completes (use session_id `"sess-fintech-001"` and iteration 1).

4. **`payment_handler.go`** — Updated with fixes applied based on the simulated user decisions.
