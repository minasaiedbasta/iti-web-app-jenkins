#!/usr/bin/env groovy
pipeline {
    agent any
    
    stages {
        stage('Build and Push Docker Image') {
            when {
                expression {
                    BRANCH_NAME == 'release'
                }
            }
            steps {
                echo "Building docker image..."
                script {
                    withCredentials ([usernamePassword(credentialsId:'dockerhub',usernameVariable: 'USER',passwordVariable:'PASS')]) {
                        sh 'docker build -t minasaiedbasta/web-app-jenkins:1.0 .'
                        sh 'echo $PASS | docker login -u $USER --password-stdin'
                        sh 'docker push minasaiedbasta/web-app-jenkins:1.0'
                    }                    
                }

            }
        }
        
        stage('Deploy Docker Image') {
            when {
                expression {
                    BRANCH_NAME == 'dev' || BRANCH_NAME == 'test' || BRANCH_NAME == 'prod'
                }
            }
            steps {
                echo 'Deploy the released Docker image'
                script {
                    withCredentials([file(credentialsId: 'kubernetes_config', variable: 'KUBECONFIG')]) {
                        sh "kubectl apply -f Deployment --kubeconfig ${KUBECONFIG}"
                    }
                }
            }
        }
    }
}