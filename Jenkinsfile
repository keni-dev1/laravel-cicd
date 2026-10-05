pipeline {
    agent any

    stages {
        stage('Get code') {
            steps {
                checkout scm
            }
        }

        stage('List files') {
            steps {
                sh 'ls -la'
            }
        }
    }
}
