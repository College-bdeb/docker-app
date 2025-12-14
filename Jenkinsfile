pipeline {
    agent any

    environment {
        DOCKERHUB_USER = "votre_user"
        DOCKERHUB_REPO = "jenkins-ci-demo"
        APP_VERSION = "1.0.${BUILD_NUMBER}"
        DOCKERHUB_TOKEN = credentials('dockerhub-token')
    }

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/votre-repo/docker-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                docker build -t $DOCKERHUB_USER/$DOCKERHUB_REPO:$APP_VERSION .
                """
            }
        }

        stage('Login to DockerHub') {
            steps {
                sh """
                echo $DOCKERHUB_TOKEN | docker login -u $DOCKERHUB_USER --password-stdin
                """
            }
        }

        stage('Push Image') {
            steps {
                sh """
                docker push $DOCKERHUB_USER/$DOCKERHUB_REPO:$APP_VERSION
                """
            }
        }

        stage('Deploy') {
            steps {
                sh """
                docker pull $DOCKERHUB_USER/$DOCKERHUB_REPO:$APP_VERSION
                docker stop myapp || true
                docker rm myapp || true
                docker run -d --name myapp -p 8080:80 $DOCKERHUB_USER/$DOCKERHUB_REPO:$APP_VERSION
                """
            }
        }
    }
}
