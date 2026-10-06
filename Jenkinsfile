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

        stage('Trivy files') {
            steps {
                sh 'trivy fs --scanners vuln --severity HIGH,CRITICAL --exit-code 0 .'
                sh 'trivy fs --scanners vuln --severity CRITICAL --exit-code 1 .'
            }
        }
    }
}
