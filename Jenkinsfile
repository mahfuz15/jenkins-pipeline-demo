pipeline {
    agent any
    
    environment {
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub-credentials')
        DOCKER_IMAGE_NAME = 'docker-pipeline'
        DOCKER_IMAGE_TAG = 'latest'
    }
    
    stages {
        stage('Checkout') {
            agent {
                label 'docker'
            }
            steps {
                git branch: 'main', url: 'https://github.com/mahfuz15/jenkins-pipeline-demo.git'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE_NAME:$DOCKER_IMAGE_TAG .'
            }
        }
        
        stage('Push Docker Image to DockerHub') {
            steps {
                withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_HUB_USERNAME', passwordVariable: 'DOCKER_HUB_PASSWORD']]) {
                    sh 'docker login -u $DOCKER_HUB_USERNAME -p $DOCKER_HUB_PASSWORD'
                    sh 'docker push $DOCKER_IMAGE_NAME:$DOCKER_IMAGE_TAG'
                }
            }
        }
    }
    
    post {
        always {
            node(label: 'docker') {
                sh 'docker logout'
            }
        }
        
        success {
            node(label: 'docker') {
                sh 'echo "Docker image build and push successful!"'
            }
        }
        
        failure {
            node(label: 'docker') {
                sh 'echo "Docker image build or push failed!"'
            }
        }
    }
}
