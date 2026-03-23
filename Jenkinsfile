pipeline {
    agent any

    stages {
        stage('SonarQube Scan') {
            steps {
                sh '''
                docker run --rm \
                -v $(pwd):/usr/src \
                sonarsource/sonar-scanner-cli \
                -Dsonar.projectKey=devops-project \
                -Dsonar.sources=. \
                -Dsonar.host.url=http://host.docker.internal:9000 \
                -Dsonar.login=squ_d62373d97c729af87c5da2cbac6118f601bb4dc6
                '''
            }
        }

        stage('Build backend image') {
            steps {
                dir('backend') {
                    sh 'docker build -t marouanelmez/devops-app-backend:latest .'
                }
            }
        }

        stage('Build frontend image') {
            steps {
                dir('frontend') {
                    sh 'docker build -t marouanelmez/devops-app-frontend:latest .'
                }
            }
        }

        stage('Push backend image') {
            steps {
                sh 'docker push marouanelmez/devops-app-backend:latest'
            }
        }

        stage('Push frontend image') {
            steps {
                sh 'docker push marouanelmez/devops-app-frontend:latest'
            }
        }

        stage('Create deploy compose') {
            steps {
                writeFile file: 'docker-compose.deploy.yml', text: '''
services:
  db:
    image: mariadb:10.6.4-focal
    restart: always
    environment:
      MYSQL_DATABASE: example
      MYSQL_ROOT_PASSWORD: mysecretpassword
    volumes:
      - db-data:/var/lib/mysql

  backend:
    image: marouanelmez/devops-app-backend:latest
    depends_on:
      - db
    ports:
      - "80:80"

  frontend:
    image: marouanelmez/devops-app-frontend:latest
    depends_on:
      - backend
    ports:
      - "3000:80"

volumes:
  db-data:
'''
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker-compose -f docker-compose.deploy.yml down || true'
                sh 'docker-compose -f docker-compose.deploy.yml up -d'
                sh 'docker ps'
            }
        }
    }
}