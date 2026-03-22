pipeline {
    agent any

    stages {

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t marouanelmez/devops-app:latest .'
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'docker push marouanelmez/devops-app:latest'
            }
        }

        stage('Deploy App') {
            steps {
                sh 'docker compose up -d'
            }
        }

    }
}