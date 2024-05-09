pipeline {
    agent any

    environment {
        DOCKERHB_CREDENTIALS = credentials('dockerhub')
    }

    stages {
        stage('Build Docker Images') {
            steps {
                sh 'docker build -t dinhcam89/retail-store-sample-catalog:0.7.1 .'
                sh 'docker build -t dinhcam89/retail-store-sample-cart:0.7.1 .'
                sh 'docker build -t dinhcam89/retail-store-sample-orders:0.7.1 .'
                sh 'docker build -t dinhcam89/retail-store-sample-checkout:0.7.1 .'
                sh 'docker build -t dinhcam89/retail-store-sample-assets:0.7.1 .'
                sh 'docker build -t dinhcam89/retail-store-sample-ui:0.7.1 .'
            }
        }
        stage('View Images') {
            steps {
                sh 'docker images'
            }
        }
        stage('Push Images to Docker Hub') {
            // some block
            steps {
                sh 'docker push dinhcam89/retail-store-sample-catalog:0.7.1'
                sh 'docker push dinhcam89/retail-store-sample-cart:0.7.1'
                sh 'docker push dinhcam89/retail-store-sample-orders:0.7.1'
                sh 'docker push dinhcam89/retail-store-sample-checkout:0.7.1'
                sh 'docker push dinhcam89/retail-store-sample-assets:0.7.1'
                sh 'docker push dinhcam89/retail-store-sample-ui:0.7.1'
            }
        }
        stage('Deploy to EKS Cluster') {
            steps {
                sh 'aws eks --region us-east-1 update-kubeconfig --name demo'
                sh 'kubectl apply -f deployment.yml'
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}