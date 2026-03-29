# DevOps Pipeline Assignment

A minimal DevOps pipeline for a Python Flask web application.
Covers CI/CD (GitHub Actions), Docker, and Infrastructure as Code (Terraform).

---

## Project Structure

```
devops-pipeline-assignment/
├── app.py                        # Flask web application
├── test_app.py                   # Pytest test file
├── requirements.txt              # Runtime dependency (Flask only)
├── requirements-dev.txt          # CI/dev dependencies (pytest, flake8)
├── Dockerfile                    # Docker image definition
├── docker-compose.yml            # Runs the container locally
├── .github/
│   └── workflows/
│       └── ci.yml                # GitHub Actions pipeline
└── terraform/
    └── main.tf                   # Mock AWS infrastructure
```
---

## Part 1 — Run with Docker

**Step 1 — Make sure Docker Desktop is running**

**Step 2 — Build and start the container**

**Step 3 — Stop the container**

---

## Part 3 — CI/CD Pipeline (GitHub Actions)

This runs automatically when you push to the `main` branch.
You do not need to trigger it manually.

**Step 1 — Push your code to GitHub**

**Step 2 — Watch the pipeline run**

**Step 3 — Understand what each step does**

**Step 4 — View logs for a failed step**

## Part 4 — Terraform (Mock Infrastructure)

This is a mock and it does not create real AWS resources.
It demonstrates what the infrastructure would look like.

**Step 1 — Navigate to the terraform directory**

**Step 2 — Initialize Terraform**


**Step 3 — Preview what would be created**

**Step 4 — Apply (mock)**

**Step 5 — Clean up**

---
