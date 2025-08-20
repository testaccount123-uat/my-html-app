pipeline {
    agent {
        kubernetes {
            label 'jenkins-agent'
        }
    }
    environment {
        DOCKER_IMAGE = 'YOUR_DOCKERHUB_USERNAME/my-html-app'
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/YOUR_USERNAME/my-html-app.git', credentialsId: 'github-creds'
            }
        }
        stage('Build Docker Image') {
            steps {
                container('docker') {
                    sh 'docker build -t ${DOCKER_IMAGE}:${BUILD_NUMBER} .'
                }
            }
        }
        stage('Push to Docker Hub') {
            steps {
                container('docker') {
                    withCredentials([usernamePassword(credentialsId: 'docker-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh 'echo ${DOCKER_PASS} | docker login -u ${DOCKER_USER} --password-stdin'
                        sh 'docker push ${DOCKER_IMAGE}:${BUILD_NUMBER}'
                        sh 'docker tag ${DOCKER_IMAGE}:${BUILD_NUMBER} ${DOCKER_IMAGE}:latest'
                        sh 'docker push ${DOCKER_IMAGE}:latest'
                    }
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                withKubeConfig([credentialsId: 'kubeconfig']) {
                    sh 'kubectl apply -f deployment.yaml'
                }
            }
        }
    }
}
