pipeline {
    agent any

    environment {
        DOCKERHB_CREDENTIALS = credentials('dockerhub')
    }

    stages {
        stage('Build Docker Images') {
            steps {
                sh "chmod +x -R ${env.WORKSPACE}"
                sh 'scripts/build-image.sh -s assets -t camECR'
                sh 'scripts/build-image.sh -s cart -t camECR'
                sh 'scripts/build-image.sh -s catalog -t camECR'
                sh 'scripts/build-image.sh -s checkout -t camECR'
                sh 'scripts/build-image.sh -s orders -t camECR'
                sh 'scripts/build-image.sh -s ui -t camECR'
            }
        }
        stage('View Images') {
            steps {
                sh 'docker images'
            }
        }
        // stage('Push Images to Docker Hub') {
        //     // some block
        //     steps {
        //         sh 'docker push dinhcam89/retail-store-sample-catalog'
        //         sh 'docker push dinhcam89/retail-store-sample-cart'
        //         sh 'docker push dinhcam89/retail-store-sample-orders'
        //         sh 'docker push dinhcam89/retail-store-sample-checkout'
        //         sh 'docker push dinhcam89/retail-store-sample-assets'
        //         sh 'docker push dinhcam89/retail-store-sample-ui'
        //     }
        // }
        // stage('Deploy to EKS Cluster') {
        //     steps {
        //         sh 'aws eks --region us-east-1 update-kubeconfig --name demo'
        //         sh 'kubectl apply -f deployment.yml'
        //     }
        // }
    }
    post {
        always {
            cleanWs()
        }
    }
}