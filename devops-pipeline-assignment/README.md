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
├── Dockerfile                    # Container build instructions
├── docker-compose.yml            # Run container locally
├── .github/
│   └── workflows/
│       └── ci.yml                # GitHub Actions pipeline
└── terraform/
    └── main.tf                   # Mock AWS infrastructure
```

---

## Prerequisites

Before you start, make sure you have these installed on your machine:

| Tool | Purpose | Check if installed |
|---|---|---|
| Git | Version control | `git --version` |
| Python 3.9+ | Run app locally | `python --version` |
| Docker Desktop | Build and run containers | `docker --version` |
| Terraform | Infrastructure as Code | `terraform --version` |

Install links:
- Git: https://git-scm.com/downloads
- Python: https://www.python.org/downloads
- Docker Desktop: https://www.docker.com/products/docker-desktop
- Terraform: https://developer.hashicorp.com/terraform/install

---

## Part 1 — Run the App Locally (No Docker)

This confirms the app works before touching Docker or CI.

**Step 1: Clone the repository**
```bash
git clone https://github.com/<your-username>/devops-pipeline-assignment.git
cd devops-pipeline-assignment
```

**Step 2: Create a virtual environment**
```bash
python -m venv venv
```

Activate it:
- On Windows: `venv\Scripts\activate`
- On Mac/Linux: `source venv/bin/activate`

**Step 3: Install all dependencies**
```bash
pip install -r requirements.txt -r requirements-dev.txt
```

**Step 4: Run the app**
```bash
python app.py
```

Open your browser and go to: `http://localhost:5000`

You should see:
```
Hello, DevOps World!
```

**Step 5: Run the tests**
```bash
pytest
```

Expected output:
```
1 passed in 0.XXs
```

**Step 6: Run the linter**
```bash
flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics
```

Expected output:
```
0
```
Zero means no errors.

---

## Part 2 — Run with Docker

This packages the app into a container and runs it.

**Step 1: Make sure Docker Desktop is running**

Open Docker Desktop. Wait until it says "Engine running" in the bottom left.

**Step 2: Build the Docker image**
```bash
docker build -t sample-web-app:latest .
```

What this does: reads your `Dockerfile`, installs Flask inside the container, copies `app.py` in, and creates an image tagged `sample-web-app:latest`.

Verify the image was created:
```bash
docker images
```

You should see `sample-web-app` in the list.

**Step 3: Run the container**
```bash
docker run -p 5000:5000 sample-web-app:latest
```

The `-p 5000:5000` maps port 5000 on your machine to port 5000 inside the container.

Open your browser: `http://localhost:5000`

You should see:
```
Hello, DevOps World!
```

**Step 4: Stop the container**

Press `Ctrl + C` in the terminal.

---

## Part 3 — Run with Docker Compose

Docker Compose is a cleaner way to run the container with all settings pre-configured.

**Step 1: Start the app with compose**
```bash
docker compose up
```

This reads `docker-compose.yml`, builds the image if needed, and starts the container.

Open your browser: `http://localhost:5000`

**Step 2: Run in the background (detached mode)**
```bash
docker compose up -d
```

**Step 3: Check it is running**
```bash
docker compose ps
```

**Step 4: View logs**
```bash
docker compose logs
```

**Step 5: Stop and remove the container**
```bash
docker compose down
```

---

## Part 4 — Set Up the CI/CD Pipeline (GitHub Actions)

The pipeline runs automatically on every push to `main`. Here is how to set it up.

**Step 1: Create a GitHub repository**

Go to https://github.com/new and create a new repository named `devops-pipeline-assignment`.

**Step 2: Push your code to GitHub**
```bash
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/<your-username>/devops-pipeline-assignment.git
git push -u origin main
```

**Step 3: Watch the pipeline run**

1. Go to your repository on GitHub
2. Click the **Actions** tab
3. You will see a workflow called **CI/CD Pipeline** running

**Step 4: Understand what each step does**

| Step | What it runs | What it checks |
|---|---|---|
| Checkout | `actions/checkout@v3` | Downloads your code onto the runner |
| Setup Python | `actions/setup-python@v4` | Installs Python 3.9 on the runner |
| Install Dependencies | `pip install` | Installs Flask + pytest + flake8 |
| Lint | `flake8` | Fails if there are syntax or import errors |
| Run Tests | `pytest` | Fails if any test fails |
| Build Docker Image | `docker build` | Fails if Dockerfile has errors |
| Mock Deploy | `echo` | Simulates a deploy (prints a message) |

**Step 5: Trigger the pipeline again**

Make any small change and push:
```bash
echo "# test" >> README.md
git add .
git commit -m "Trigger CI"
git push
```

Go to the Actions tab and watch it run again.

**Step 6: See what a failed pipeline looks like (optional)**

Introduce a deliberate error in `app.py`:
```python
undefined_variable  # add this line anywhere
```

Push it. The **Lint** step will fail and the pipeline stops. Fix it and push again — it goes green.

---

## Part 5 — Terraform (Mock Infrastructure)

This provisions mock AWS infrastructure. Because we use mock credentials, no real AWS account is needed and nothing gets charged.

**Step 1: Go into the terraform directory**
```bash
cd terraform
```

**Step 2: Initialise Terraform**
```bash
terraform init
```

This downloads the AWS provider plugin. You will see:
```
Terraform has been successfully initialized!
```

**Step 3: Preview what will be created**
```bash
terraform plan
```

Terraform will show you a list of resources it would create:
- `aws_vpc.main_vpc` — the private network
- `aws_subnet.main_subnet` — a subnet inside the VPC
- `aws_instance.web_server` — an EC2 virtual machine
- `aws_s3_bucket.app_storage` — an S3 storage bucket

Since we are using mock credentials with `skip_credentials_validation = true`, the plan runs without hitting real AWS.

**Step 4: Apply (simulate provisioning)**
```bash
terraform apply
```

Type `yes` when prompted.

> Note: With mock credentials this may error at the apply stage since it cannot actually call the AWS API. That is expected and acceptable for this assignment. The `plan` output is what demonstrates the IaC configuration is correct.

**Step 5: Go back to the project root**
```bash
cd ..
```

---

## Quick Reference — All Commands

```bash
# Run app locally
python app.py

# Run tests
pytest

# Run linter
flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics

# Docker: build image
docker build -t sample-web-app:latest .

# Docker: run container
docker run -p 5000:5000 sample-web-app:latest

# Docker Compose: start
docker compose up

# Docker Compose: start in background
docker compose up -d

# Docker Compose: stop
docker compose down

# Terraform: initialise
cd terraform && terraform init

# Terraform: preview changes
terraform plan

# Terraform: apply changes
terraform apply
```

---

## Troubleshooting

**Port 5000 already in use**
```bash
# On Mac/Linux:
lsof -i :5000
# On Windows:
netstat -ano | findstr :5000
```
Kill that process, or change the port in `app.py` and `docker-compose.yml` to `5001`.

**Docker build fails**

Make sure Docker Desktop is running. Check with:
```bash
docker info
```

**pytest: ModuleNotFoundError**

You are probably not in the virtual environment. Run:
```bash
source venv/bin/activate   # Mac/Linux
venv\Scripts\activate      # Windows
```

**GitHub Actions fails on the lint step**

Run flake8 locally first to see the errors:
```bash
flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics
```
Fix the errors, then push again.

**Terraform init fails**

Make sure Terraform is installed:
```bash
terraform --version
```
If not installed, download from https://developer.hashicorp.com/terraform/install

---

## Self-Assessment

Rating scale: 0 = No experience, 1 = Beginner, 2 = Familiar, 3 = Comfortable, 4 = Proficient, 5 = Expert

| Tool / Area | Rating (out of 5) | Comments / Example Projects |
|---|---|---|
| CI/CD (GitHub Actions, Jenkins) | 3 | Worked in projects to automate the pipelines. |
| Docker | 1 | |
| Kubernetes | 1 | |
| Terraform / Ansible | 1 | |
| Monitoring Tools (Prometheus, Grafana, ELK) | 0 | |
| Git | 3 | Regularly use it to push code to remote repositories. |
