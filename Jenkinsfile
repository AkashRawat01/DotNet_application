pipeline {
    agent any
    environment {
        KUBECONFIG = '/var/lib/jenkins/.kube/config'
        IMAGE_NAME = 'blue-harvest-images'
        AWS_REGION = 'us-east-1'
        ECR_REPOSITORY = '166000963935.dkr.ecr.us-east-1.amazonaws.com'
        IMAGE_TAG = 'DotNet_app'
    }
    stages {
        stage('Checkout SCM') {
            steps {
                script {
                    sh 'rm -rf *'
                    checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/AkashRawat01/DotNet_application.git']]) //Generate  using pipeline syntax
                }
            }
        }
        stage('Select EKS Cluster') {
            steps {
                sh 'echo "Selected EKS Cluster is ${EKS_Cluster}"'
                sh 'kubectl config use-context ${EKS_Cluster}'
                sh 'kubectl config current-context'
            }
        }
        stage('Check Kubernetes Nodes') {
            steps {
                sh 'kubectl get nodes'
            }
        }
        stage('Docker Build') {
            when {
                environment name: 'Deploy_DotNet_application', value: 'true'
            }
            steps {
                script {
                    sh '''#!/bin/bash
                    aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${ECR_REPOSITORY}
                    docker build -t ${IMAGE_NAME}:latest .
                    docker tag ${IMAGE_NAME}:latest ${ECR_REPOSITORY}/${IMAGE_NAME}:${IMAGE_TAG}_${BUILD_NUMBER}
                    docker push ${ECR_REPOSITORY}/${IMAGE_NAME}:${IMAGE_TAG}_${BUILD_NUMBER}
                    '''
                }
            }
        }
        stage('Installing DotNET Application on EKS cluster') {
            when {
                environment name: 'Deploy_DotNet_application', value: 'true'
            }
            steps {
                script {
                    //sh 'kubectl create ns application'
                    sh '''
                    helm install -n application my-dotnet-app ./my-dotnet-app \
                        --set image.repository=${ECR_REPOSITORY}/${IMAGE_NAME} \
                        --set image.tag=${IMAGE_TAG}_${BUILD_NUMBER} \
                        --set image.pullSecrets[0].name=ecr-registry-secret
                    '''
                }
            }
        }
        stage('Uninstalling DotNET Application on EKS cluster') {
            when {
                environment name: 'Uninstall_DotNet_application', value: 'true'
            }
            steps {
                script {
                    sh 'helm uninstall -n application my-dotnet-app'
                    //sh 'kubectl delete ns application'
                }
            }
        }
    }
    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}
