pipeline {
    agent any

    environment {
        IMAGE_NAME = "ibrahimdarghouthi1919/python-app"
        REGISTRY = "docker.io"
        GIT_REPO = "https://github.com/Barhoum1919/DevSecOps_pipeline.git"  
        GITOPS_REPO = "git@github.com:Barhoum1919/sec_gitops.git"            
        VAULT_SECRET_GITHUB = 'secret/github'
        VAULT_SECRET_DOCKERHUB = 'secret/dockerhub'
        VAULT_SECRET_GITOPS = 'secret/gitops'
        VAULT_SECRET_SONAR = 'secret/sonarqube' 
    }

    stages {

        stage('Checkout Code') {
            steps {
                script {
                    withVault([vaultSecrets: [[path: "${VAULT_SECRET_GITHUB}", secretValues: [[envVar: 'GITHUB_TOKEN', vaultKey: 'token']]]]]) {
                        checkout([$class: 'GitSCM',
                            branches: [[name: '*/main']],
                            userRemoteConfigs: [[
                                url: "${GIT_REPO}",
                                credentials: [username: 'Barhoum1919', password: "${GITHUB_TOKEN}"]
                            ]]
                        ])
                    }
                }
            }
        }

        stage('Prepare') {
            steps {
                script {
                    env.IMAGE_TAG = sh(script: "git rev-parse --short HEAD", returnStdout: true).trim()
                }
            }
        }

        stage('SonarQube Analysis') {
            when {
                expression { return env.VAULT_SECRET_SONAR != null && env.VAULT_SECRET_SONAR != '' }
            }
            steps {
                script {
                    withVault([vaultSecrets: [[path: "${VAULT_SECRET_SONAR}", secretValues: [[envVar: 'SONAR_TOKEN', vaultKey: 'token']]]]]) {
                        def scannerHome = tool name: 'SonarQube Scanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
                        withSonarQubeEnv('SonarQube') {
                            sh """
                                ${scannerHome}/bin/sonar-scanner \
                                -Dsonar.projectKey=python-app \
                                -Dsonar.sources=. \
                                -Dsonar.login=$SONAR_TOKEN
                            """
                        }
                    }
                }
            }
        }

        stage('Dependency Scan with Grype') {
            steps {
                sh "grype . -o table > grype-deps-report.txt || true"
                sh "cat grype-deps-report.txt"
            }
        }

        stage('Security Scan with Trivy (FS)') {
            steps {
                sh "trivy fs --scanners vuln --no-progress --severity HIGH,CRITICAL --format table --output trivy-fs-report.txt . || true"
                sh "cat trivy-fs-report.txt"
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                }
            }
        }

        stage('Scan Docker Image') {
            steps {
                script {
                    sh """
                    trivy image --timeout 10m \
                    --scanners vuln \
                    --no-progress \
                    --severity HIGH,CRITICAL \
                    --format table \
                    --output trivy-report.txt \
                    ${IMAGE_NAME}:${IMAGE_TAG} || true
                    """
                    sh 'cat trivy-report.txt'
                }
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                script {
                    withVault([vaultSecrets: [[path: "${VAULT_SECRET_DOCKERHUB}", secretValues: [
                        [envVar: 'DOCKER_USER', vaultKey: 'username'],
                        [envVar: 'DOCKER_PASS', vaultKey: 'password']
                    ]]]]) {
                        sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                        sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
                    }
                }
            }
        }

        stage('GitOps Update') {
            steps {
                script {
                    withVault([vaultSecrets: [[path: "${VAULT_SECRET_GITOPS}", secretValues: [[envVar: 'SSH_KEY', vaultKey: 'ssh_private_key']]]]]) {
                        sh 'rm -rf temp-repo'
                        sh "mkdir -p ~/.ssh && echo \"$SSH_KEY\" > ~/.ssh/id_rsa && chmod 600 ~/.ssh/id_rsa"
                        sh "ssh-keyscan github.com >> ~/.ssh/known_hosts"
                        sh "git clone ${GITOPS_REPO} temp-repo"
                        dir('temp-repo') {
                            sh "sed -i 's|image: .*|image: ${IMAGE_NAME}:${IMAGE_TAG}|' k8s/deployment.yaml"
                            def changes = sh(script: "git status --porcelain", returnStdout: true).trim()
                            if (changes) {
                                sh "git add ."
                                sh "git commit -m 'Update image tag to ${IMAGE_TAG}'"
                                sh "git push origin main"
                            } else {
                                echo "No changes detected in GitOps repo."
                            }
                        }
                    }
                }
            }
        }

        stage('Sync ArgoCD') {
            steps {
                script {
                    sh "argocd app sync python-app"  
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
