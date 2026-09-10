# MLOps Demo Application

This project is a simple Flask-based machine learning inference service deployed with Docker and GitHub Actions.

## Features

- Health endpoint for system checks
- Prediction endpoint for inference
- Dockerized deployment
- CI/CD pipeline with automated testing and deployment
- Staging deployment with smoke test
- Production approval gate using GitHub Environments

## Project Structure

- `app.py` – Flask application
- `tests/test_app.py` – application tests
- `Dockerfile` – container image definition
- `compose.yaml` – local Docker Compose configuration
- `.github/workflows/cd.yml` – GitHub Actions deployment workflow
- `requirements.txt` – Python dependencies

## Local Development

Create a virtual environment and install dependencies:

```bash
python3 -m venv .venv
. .venv/bin/activate
python -m pip install -r requirements.txt
```

Run the app locally:

```bash
python app.py
```

Open:

- `http://localhost:5000/health`
- `http://localhost:5000/`

Run tests:

```bash
pytest -q
```

## Docker

Build the image:

```bash
docker build -t mlops-demo .
```

Run the container:

```bash
docker run -d --name mlops-api -p 5000:5000 mlops-demo
```

Check health:

```bash
curl http://localhost:5000/health
```

## Continuous Delivery Workflow

The workflow in `.github/workflows/cd.yml` performs the following steps:

1. Run tests
2. Build Docker image
3. Push image to GitHub Container Registry
4. Deploy to staging over SSH
5. Perform a smoke test against `/health`
6. Wait for manual approval in the `production` environment
7. Deploy to production once approved

## Required GitHub Secrets

Add these repository secrets under GitHub → Settings → Secrets and variables → Actions:

- `STAGING_HOST`
- `STAGING_USER`
- `STAGING_SSH_KEY`
- `PRODUCTION_HOST`
- `PRODUCTION_USER`
- `PRODUCTION_SSH_KEY`

Do not store private keys in YAML files. Keep them in GitHub Secrets only.

## GitHub Environments

Create two environments:

- `staging`
- `production`

For `production`, enable required reviewers to require manual approval before deployment.

## Deployment Trigger

Push a version tag to trigger the workflow:

```bash
git checkout main
git tag v1.0.0
git push origin v1.0.0
```

## Notes

This project demonstrates a simple continuous delivery flow for a containerized API service using automated validation, staging deployment, health verification, and a production approval gate.
