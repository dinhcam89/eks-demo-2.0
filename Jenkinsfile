pipeline {
    agent any

    environment {
        DOCKERHB_CREDENTIALS = credentials('dockerhub')
    }

    stages {
        // stage('SCA with OWASP Dependency Check') {
        //     steps {
        //         dependencyCheck additionalArguments: '''--format HTML
        //     ''', odcInstallation: 'Dependency-Check'
        //     }
        // }
        stage('SonarQube Analysis') {
            steps {
                script {
                    // requires SonarQube Scanner 2.8+
                    scannerHome = tool 'SonarScanner'
                }
                withSonarQubeEnv('SonarQube Server') {
                    sh "${scannerHome}/bin/sonar-scanner \
                    -Dsonar.projectKey=retail-shop-microservices \
                    -Dsonar.java.binaries=."
                }
            }
        }
        stage('Login to Docker Hub') {
            steps {
                sh 'sudo su - jenkins'
                sh 'echo $DOCKERHB_CREDENTIALS_PSW | echo $DOCKERHB_CREDENTIALS_USR | docker login -u $DOCKERHB_CREDENTIALS_USR -p $DOCKERHB_CREDENTIALS_PSW'
            }
        }
        stage('Build Docker Images') {
            steps {
                sh "chmod +x -R ${env.WORKSPACE}"
                sh 'scripts/build-image.sh -s assets -t latest'
                sh 'scripts/build-image.sh -s cart -t latest'
                sh 'scripts/build-image.sh -s catalog -t latest'
                sh 'scripts/build-image.sh -s checkout -t latest'
                sh 'scripts/build-image.sh -s orders -t latest'
                sh 'scripts/build-image.sh -s ui -t latest'
            }
        }
        stage('View Images') {
            steps {
                sh 'docker images'
            }
        }
        stage('Push Images to Docker Hub') {
            steps {
                sh 'docker push dinhcam89/dinhcam89-catalog:latest'
                sh 'docker push dinhcam89/dinhcam89-cart:latest'
                sh 'docker push dinhcam89/dinhcam89-orders:latest'
                sh 'docker push dinhcam89/dinhcam89-checkout:latest'
                sh 'docker push dinhcam89/dinhcam89-assets:latest'
                sh 'docker push dinhcam89/dinhcam89-ui:latest'
            }
        }
        // stage('Deploy and Configure ArgoCD') {
        //     steps {
        //         sh 'aws eks --region ap-southeast-1 update-kubeconfig --name eks-cicd-staging'
        //         sh 'kubectl create namespace argocd'
        //         sh 'kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/v2.4.7/manifests/install.yaml'
        //         sh 'kubectl wait --for=condition=Available deployment/argocd-server -n argocd --timeout=300s'
        //         sh 'kubectl patch svc argocd-server -n argocd -p \'{"spec": {"type": "LoadBalancer"}}\''
        //     }
        // }
        // stage('Deploy to Staging Environment') {
        //     steps {
        //         sh 'aws eks --region ap-southeast-1 update-kubeconfig --name eks-cicd-staging'
        //         sh 'kubectl apply -f dist/kubernetes/deploy.yaml'
        //     }
        // }
        // stage('Deploy to Production Environment') {
        //     steps {
        //         script {
        //             try {
        //                 timeout(time: 5, unit: 'MINUTES') {
        //                     env.useChoice = input message: 'Can it be deployed to the production environment?', ok: 'Yes',
        //                     parameters: [choice(name: 'useChoice', choices: 'Yes\nNo', description: 'Choose whether to deploy to production or not')]
        //                 }
        //                 if (env.useChoice == 'Yes') {
        //                     sh 'sudo su - jenkins'
        //                     sh 'aws eks --region ap-southeast-1 update-kubeconfig --name eks-cicd-prod'
        //                     sh 'kubectl apply -f dist/kubernetes/deploy.yaml'
        //                 } else {
        //                     echo 'The deployment is not allowed to the production environment'
        //                 }
        //             }
        //             catch (Exception err) {
        //             // handle the exception
        //             }
        //         }
        //     }
        // }
        stage('Scan Docker Images with Trivy') {
            steps {
                sh 'TMPDIR=/home/jenkins'
                sh 'trivy dinhcam89/dinhcam89-catalog:latest'
                sh 'trivy dinhcam89/dinhcam89-cart:latest'
                sh 'trivy dinhcam89/dinhcam89-orders:latest'
                sh 'trivy dinhcam89/dinhcam89-checkout:latest'
                sh 'trivy dinhcam89/dinhcam89-assets:latest'
                sh 'trivy dinhcam89/dinhcam89-ui:latest'
            }
        }
    }
    post {
        always {
            cleanWs()
            // sh 'docker rmi -f $(docker images -aq)'
            sh 'docker logout'
        }
    }
}
