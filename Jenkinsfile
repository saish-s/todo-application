pipeline {
    agent any
    
    environment {
        GIT_CRDENTIALS_ID = "github-credentails"
        DOCKER_CREDENTAILS_ID = "docker-hub-credentails"
        GIT_REPO = "https://github.com/saish-s/todo-application.git"
        DOCKER_IMAGE = "saishshindepersistent/todo-application:latest"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'master', credentialsId: env.GIT_CRDENTIALS_ID, url: "${GIT_REPO}"
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ${DOCKER_IMAGE} .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: "${env.DOCKER_CREDENTAILS_ID}"]) {
                    sh 'docker push ${DOCKER_IMAGE}'
                }
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                sh 'docker-compose down'
                sh 'docker-compose up -d'
            }
        }

        stage('Verify Services') {
            steps {
                sh 'docker ps'
            }
        }

        stage('Clean Workspace') {
            steps {
                sh 'rm -rf *'
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful! Application should e available at http://localhost:8082'
        }
        failure {
            echo 'Deployment Failed!'
        }
    }
}
