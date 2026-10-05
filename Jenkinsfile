pipeline {
    agent any

    stages {
        stage('Get code') {
            steps {
                checkout scm
            }
        }

        stage('Gitleaks') {
            steps {
                sh 'gitleaks detect --source . --no-banner --redact --exit-code 1'
            }
        }
    }
}
