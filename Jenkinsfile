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
                        sh '''
                            docker build -t minasaiedbasta/web-app-jenkins:1.0 .
                            echo $PASS | docker login -u $USER --password-stdin
                            docker push minasaiedbasta/web-app-jenkins:1.0
                            echo ${BUILD_NUMBER} > ../build.txt
                        '''
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
                        sh '''
                            export BUILD_NUMBER=$(cat ../build.txt)
                            mv Deployment/deploy.yaml Deployment/deploy.yaml.tmp
                            cat Deployment/deploy.yaml.tmp | envsubst > Deployment/deploy.yaml
                            rm -f Deployment/deploy.yaml.tmp
                            kubectl apply -f Deployment --kubeconfig ${KUBECONFIG} -n ${BRANCH_NAME}
                        '''
                    }
                }
            }
        }
    }
}