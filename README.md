DevSecOps Project: Vault and Trivy Integration

This project demonstrates a DevSecOps workflow using HashiCorp Vault for secrets management
and Trivy for container image vulnerability scanning. The goal was to enhance CI/CD pipelines
with secure secret handling and automated security checks.

Project Structure:

- vault/          # Vault configuration and setup
- trivy/          # Trivy scanning scripts and configurations
- docker/         # Dockerfiles for sample applications
- ci-cd/          # CI/CD pipeline configuration files (GitHub Actions, etc.)

Features:

1. Vault Integration:
   - Store and manage secrets securely
   - Provide secrets to Docker containers at runtime
   - Demonstrate secure authentication with Vault

2. Trivy Vulnerability Scanning:
   - Scan Docker images for known vulnerabilities
   - Integrate scans into CI/CD pipelines
   - Generate reports for visibility and compliance

3. CI/CD Security Workflow:
   - Automatically scan images before deployment
   - Fail the pipeline if critical vulnerabilities are found
   - Retrieve secrets from Vault securely during deployment

How to Run:

1. Start Vault and configure policies:
   - Follow the instructions in the `vault/` folder

2. Build and scan Docker images:
   - `docker build -t sample-app ./docker`
   - `trivy image sample-app`

3. Run CI/CD pipeline (example with GitHub Actions):
   - Push your code to GitHub
   - Pipeline will retrieve secrets from Vault and scan images with Trivy




