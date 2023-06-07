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
                echo 'Build and push the Docker image'
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
            }
        }
    }
}