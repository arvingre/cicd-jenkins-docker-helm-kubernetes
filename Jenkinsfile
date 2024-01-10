pipeline {
    
    tools {
        maven 'Maven3'
    }
    agent any
    
    environment {
        registry = "934891041601.dkr.ecr.ap-southeast-1.amazonaws.com/demo-cicd-repo"
    }
    stages {
        stage('Git Checkout') {
            steps {
               checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/arvingre/cicd-jenkins-docker-helm-kubernetes.git']])
            }
        }
        stage('Build Artifact') {
            steps {
               sh 'mvn clean install'
            }
        }
                stage('Unit Test') {
            steps {
               sh 'mvn test'
            }
        }
        stage('Build Docker Image') {
            steps {
               script{
                   dockerImage = docker.build registry
                   dockerImage.tag("$BUILD_NUMBER")
               }
            }
        }
        stage('Push Docker Image') {
            steps {
               script{
                   sh'aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin 934891041601.dkr.ecr.ap-southeast-1.amazonaws.com'
                   sh'docker push 934891041601.dkr.ecr.ap-southeast-1.amazonaws.com/demo-cicd-repo:$BUILD_NUMBER'
               }
            }
        }
        stage('Helm Deployment') {
            steps {
               script{
                   sh"helm upgrade first --install mychart --namespace helm-deployment --set image.tag=$BUILD_NUMBER"
                  
               }
            }
        }
    } 
}
