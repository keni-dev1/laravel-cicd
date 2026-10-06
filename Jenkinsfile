pipeline {
    agent any

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
        IMAGE_NAME = 'laravel-cicd'
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

        stage('Docker build') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$BUILD_NUMBER .'
            }
        }

        stage('Trivy image') {
            steps {
                sh 'trivy image --severity HIGH,CRITICAL --exit-code 0 $IMAGE_NAME:$BUILD_NUMBER'
                catchError(buildResult: 'FAILURE', stageResult: 'FAILURE') {
                    sh 'trivy image --severity CRITICAL --ignore-unfixed --exit-code 1 $IMAGE_NAME:$BUILD_NUMBER'
                }
            }
        }

        stage('Docker Hub push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials',
                                                  usernameVariable: 'DH_USER',
                                                  passwordVariable: 'DH_PASS')]) {
                    sh 'echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin'
                    sh 'docker tag $IMAGE_NAME:$BUILD_NUMBER $DH_USER/$IMAGE_NAME:$BUILD_NUMBER'
                    sh 'docker tag $IMAGE_NAME:$BUILD_NUMBER $DH_USER/$IMAGE_NAME:latest'
                    sh 'docker push $DH_USER/$IMAGE_NAME:$BUILD_NUMBER'
                    sh 'docker push $DH_USER/$IMAGE_NAME:latest'
                    sh 'docker logout'
                }
            }
        }
    }
}
