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

## Prerequisites

Install these before you begin.

| Tool | Purpose | Download |
|---|---|---|
| Python 3.9+ | Run the app locally | https://www.python.org/downloads |
| Docker Desktop | Build and run containers | https://www.docker.com/products/docker-desktop |
| Git | Version control | https://git-scm.com/downloads |
| Terraform | IaC (optional, mock only) | https://developer.hashicorp.com/terraform/install |

---

## Part 1 — Run the App Locally (No Docker)

Use this to verify the app works before touching Docker or CI.

**Step 1 — Clone the repository**
```bash
git clone https://github.com/your-username/devops-pipeline-assignment.git
cd devops-pipeline-assignment
```

**Step 2 — Create a virtual environment**
```bash
# Windows
python -m venv venv
venv\Scripts\activate

# macOS / Linux
python -m venv venv
source venv/bin/activate
```

**Step 3 — Install dependencies**
```bash
pip install -r requirements.txt -r requirements-dev.txt
```

**Step 4 — Run the app**
```bash
python app.py
```

Open your browser at `http://localhost:5000`
You should see: `Hello, DevOps World!`

**Step 5 — Run lint and tests manually**
```bash
# Lint (checks for syntax errors only)
flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics

# Tests
pytest
```

Both should pass with no errors before you proceed.

---

## Part 2 — Run with Docker

**Step 1 — Make sure Docker Desktop is running**

Open Docker Desktop and wait until it says "Engine running".

**Step 2 — Build and start the container**
```bash
docker compose up --build
```

What this does:
- Reads the `Dockerfile` and builds an image called `sample-web-app:latest`
- Starts a container from that image
- Maps port 5000 on your machine to port 5000 inside the container

Open your browser at `http://localhost:5000`
You should see: `Hello, DevOps World!`

**Step 3 — Stop the container**
```bash
# Press Ctrl+C in the terminal, then:
docker compose down
```

**Useful Docker commands**
```bash
# See running containers
docker ps

# See all built images
docker images

# Build image without starting it
docker build -t sample-web-app:latest .

# Run image directly (without compose)
docker run -p 5000:5000 sample-web-app:latest
```

---

## Part 3 — CI/CD Pipeline (GitHub Actions)

This runs automatically when you push to the `main` branch.
You do not need to trigger it manually.

**Step 1 — Push your code to GitHub**
```bash
git add .
git commit -m "initial devops setup"
git push origin main
```

**Step 2 — Watch the pipeline run**

1. Go to your GitHub repository
2. Click the **Actions** tab
3. You will see a workflow called **CI/CD Pipeline** running

**Step 3 — Understand what each step does**

| Step | What happens |
|---|---|
| Checkout | Downloads your code onto the GitHub runner (a temporary Ubuntu VM) |
| Setup Python | Installs Python 3.9 on the runner |
| Install Dependencies | Runs `pip install -r requirements.txt -r requirements-dev.txt` |
| Lint | Runs flake8 — fails the pipeline if there are syntax errors |
| Run Tests | Runs pytest — fails the pipeline if any test fails |
| Build Docker Image | Runs `docker build` on the runner to verify the image builds |
| Mock Deploy | Prints a deploy message — simulates deployment (no real server) |

**If a step fails**, the pipeline stops there and marks the run as failed (red X).
Fix the error, push again, and the pipeline restarts from the beginning.

**Step 4 — View logs for a failed step**

1. Click the failed run in the Actions tab
2. Click the job name `build-test-deploy`
3. Expand the failed step to see the error message

---

## Part 4 — Terraform (Mock Infrastructure)

This is a mock — it does not create real AWS resources or cost any money.
It demonstrates what the infrastructure would look like.

**Step 1 — Navigate to the terraform directory**
```bash
cd terraform
```

**Step 2 — Initialize Terraform**

This downloads the AWS provider plugin locally.
```bash
terraform init
```

Expected output: `Terraform has been successfully initialized!`

**Step 3 — Preview what would be created**
```bash
terraform plan
```

This shows you a dry run — what Terraform *would* create if run for real.
You will see three resources listed:
- `aws_vpc.main_vpc` — the private network
- `aws_subnet.main_subnet` — a subnet inside the VPC
- `aws_instance.web_server` — an EC2 virtual machine
- `aws_s3_bucket.app_storage` — an S3 storage bucket

**Step 4 — Apply (mock)**
```bash
terraform apply
```

Type `yes` when prompted.

Because the provider is configured with mock credentials and `skip_credentials_validation = true`, this will either apply in mock mode or show a simulated plan — no real AWS account needed.

**Step 5 — Clean up**
```bash
terraform destroy
```

Type `yes` when prompted. This removes everything Terraform created.

---

## Full Execution Order (Summary)

```
1. Clone the repo
2. Run locally with Python      →  verify app works
3. Run tests manually           →  verify lint + pytest pass
4. Run with Docker              →  verify container works
5. Push to GitHub               →  CI pipeline runs automatically
6. Check Actions tab            →  confirm all steps pass
7. Run terraform init + plan    →  inspect mock infrastructure
```

---

## Common Errors and Fixes

**`ModuleNotFoundError: No module named 'flask'`**
You skipped the virtual environment step or forgot to activate it.
```bash
source venv/bin/activate   # macOS/Linux
venv\Scripts\activate      # Windows
pip install -r requirements.txt -r requirements-dev.txt
```

**`Port 5000 is already in use`**
Something else is running on port 5000.
```bash
# macOS/Linux — find and kill it
lsof -i :5000
kill -9 <PID>

# Or just change the port in docker-compose.yml
ports:
  - "5001:5000"   # use localhost:5001 instead
```

**`Cannot connect to the Docker daemon`**
Docker Desktop is not running. Open it and wait for the engine to start.

**`terraform: command not found`**
Terraform is not installed or not in your PATH.
Download it from https://developer.hashicorp.com/terraform/install and follow the install instructions for your OS.

**GitHub Actions failing on the Lint step**
Run flake8 locally first to see the exact error:
```bash
flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics
```
Fix the reported line, commit, and push again.
