# Prepare Project for Automated Code Review

## Problem/Feature Description

A Go microservice that handles payment processing has just been migrated into the company's monorepo. The team wants to onboard it to the Codex-based code review workflow that other services already use. Before any reviews can be run, the project needs to be properly configured so that the review tooling has a place to write its working files, and those working files don't accidentally end up committed to version control.

A junior engineer started the project setup but only got as far as creating the basic source files. Your job is to complete the setup so that the review workflow can be used with this service. The project already has a `.gitignore` file with some entries, so be careful not to break what's already there.

## Output Specification

Configure the project so it is ready to use the Codex review workflow:
- The appropriate working directory for review artifacts must exist
- The `.gitignore` file must be updated appropriately

Write a brief `setup_notes.md` documenting what you did and why.

## Input Files

The following files are provided as inputs. Extract them before beginning.

=============== FILE: inputs/.gitignore ===============
# Binaries
*.exe
*.exe~
*.dll
*.so
*.dylib
bin/
dist/

# Test binary, built with `go test -c`
*.test

# Output of the go coverage tool
*.out

# Go workspace file
go.work

# IDE files
.idea/
.vscode/
*.swp
*.swo

# Environment files
.env
.env.local

=============== FILE: inputs/main.go ===============
package main

import (
	"log"
	"net/http"

	"github.com/company/payments-service/internal/handlers"
)

func main() {
	mux := http.NewServeMux()
	mux.HandleFunc("/pay", handlers.ProcessPayment)
	mux.HandleFunc("/health", handlers.HealthCheck)

	log.Println("Starting payments service on :8080")
	if err := http.ListenAndServe(":8080", mux); err != nil {
		log.Fatal(err)
	}
}

=============== FILE: inputs/go.mod ===============
module github.com/company/payments-service

go 1.21
