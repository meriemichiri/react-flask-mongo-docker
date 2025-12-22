pipeline {
    agent any

    stages {

        stage('Clone Repository') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/meriemichiri/react-flask-mongo-docker.git'
            }
        }

        stage('Build Docker Images') {
            steps {
                sh 'docker-compose build'
            }
        }

        stage('Run Containers (Test)') {
            steps {
                sh '''
                docker-compose up -d
                sleep 15
                docker-compose ps
                '''
            }
        }

        stage('Push Images to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_TOKEN'
                )]) {
                    sh '''
                    echo "$DOCKER_TOKEN" | docker login -u "$DOCKER_USER" --password-stdin

                    docker tag react-flask-mongo-pipeline-backend:latest $DOCKER_USER/react-flask-mongo-backend:latest
                    docker tag react-flask-mongo-pipeline-frontend:latest $DOCKER_USER/react-flask-mongo-frontend:latest

                    docker push $DOCKER_USER/react-flask-mongo-backend:latest
                    docker push $DOCKER_USER/react-flask-mongo-frontend:latest
                    '''
                }
            }
        }
    }

    post {
        always {
            sh 'docker-compose down || true'
        }
    }
}

