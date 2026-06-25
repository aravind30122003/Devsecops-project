pipeline {
    agent any

    environment {
        BUILD_NUMBER = "v1"
        IMAGE = "aravind310730/flask-app:${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/aravind30122003/Devsecops-project.git'
            }
        }

        stage('Trivy FS Scan') {
            steps {
                sh 'trivy fs .'
            }
        }

        stage('Docker Build') {
            steps {
                sh "docker build -t ${IMAGE} ."
            }
        }

        stage('Image Scan') {
            steps {
                sh "trivy image ${IMAGE}"
            }
        }

        stage('Push Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-cred',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh "docker push ${IMAGE}"
                }
            }
        }

        stage('Deploy Helm') {
            steps {
                sh """
                helm upgrade --install flask-app \
                ./helm/flask-app \
                --set image.tag=${BUILD_NUMBER}
                """
            }
        }
    }
}