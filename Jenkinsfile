pipeline {
    agent any
    environment {
        PROJECT_ID = 'multi-k8s-381107'
        CLUSTER_NAME = 'cluster-1'
        LOCATION = 'asia-southeast1-a'
        CREDENTIALS_ID = 'multi-k8s'
    }
    stages {
        stage('checkout code') {
            steps {
                checkout scm
            }
        }
        stage ('Build Image') {
            steps {
                sh 'docker build -t nguyenduynghi2001/multi-worker:${env.BUILD_ID}'
                sh 'docker build -t nguyenduynghi2001/multi-server:${env.BUILD_ID}'
                sh 'docker build -t nguyenduynghi2001/multi-client:${env.BUILD_ID}'
            }
        }
        stage('Push Image') {
            steps {
                sh 'docker login -u $DOCKER_USER -p $DOCKER_PASSWORD'
                sh 'docker push nguyenduynghi2001/multi-worker:${env.BUILD_ID}'
                sh 'docker push -t nguyenduynghi2001/multi-server:${env.BUILD_ID}'
                sh 'docker push -t nguyenduynghi2001/multi-client:${env.BUILD_ID}'
                sh 'docker logout'
            }
        }      
        stage('Deploy to GKE') {
            steps{
                sh "sed -i 's/multi-worker:latest/multi-worker:${env.BUILD_ID}/g' k8s/worker-deployment.yaml"
                sh "sed -i 's/multi-server:latest/multi-server:${env.BUILD_ID}/g' k8s/server-deployment.yaml"
                sh "sed -i 's/multi-client:latest/multi-client:${env.BUILD_ID}/g' k8s/client-deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'k8s/*', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
            }
        }
    }
}