DevSecOps Project: Vault and Trivy Integration

This project demonstrates a DevSecOps workflow using HashiCorp Vault for secrets management
and Trivy for container image vulnerability scanning. The goal was to enhance CI/CD pipelines
with secure secret handling and automated security checks.


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






