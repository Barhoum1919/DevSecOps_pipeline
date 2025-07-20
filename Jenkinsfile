pipeline {
    agent any
     environment {
        IMAGE_NAME = "rihab26/nodejs-app"
        REGISTRY = "docker.io"
        GIT_REPO = "https://github.com/RihabHaddad/DevSecOps-pipeline.git"
        GITOPS_REPO = "git@github.com:RihabHaddad/GitOps.git"
        VAULT_SECRET_GITHUB = 'secret/github'
        VAULT_SECRET_DOCKERHUB = 'secret/dockerhub'
        VAULT_SECRET_SONAR = 'secret/sonarqube'
        VAULT_SECRET_GITOPS = 'secret/gitops'
    }
    stages {
        stage('Build') {
            steps {
                //
            }
        }
        stage('Test') {
            steps {
                //
            }
        }
        stage('Deploy') {
            steps {
                //
            }
        }
    }
}
    post {
        always {
            echo 'This will always run after the stages.'
        }
        success {
            echo 'This will run only if the pipeline succeeds.'
        }
        failure {
            echo 'This will run only if the pipeline fails.'
        }
    }

    