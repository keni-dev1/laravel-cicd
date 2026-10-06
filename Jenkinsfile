pipeline {
    agent any

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }

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

        stage('SonarQube') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh '$SCANNER_HOME/bin/sonar-scanner'
                }
            }
        }
    }
}
