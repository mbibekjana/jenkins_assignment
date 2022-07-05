@Library('github.com/releaseworks/jenkinslib') _

pipeline {
    agent any
    environment {
        registry = "402521938984.dkr.ecr.us-east-1.amazonaws.com/assignment"
    }
    
    stages {
        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/mbibekjana/jenkins_assignment.git']]])
            }
        }
    // Building Docker images
    stage('Building image') {
      steps{
        script {
          def dockerHome = tool 'docker'
          env.PATH = "${dockerHome}/bin:${env.PATH}"
          dockerImage = docker.build registry
        }
      }
    }

    // Uploading Docker images into AWS ECR
    stage('Pushing to ECR') {
        steps{
            script {
                sh 'docker login -u AWS -p $(aws ecr get-login-password --region us-east-1) 402521938984.dkr.ecr.us-east-1.amazonaws.com/assignment '
                sh 'docker push 402521938984.dkr.ecr.us-east-1.amazonaws.com/assignment'
            }
        }
    }

    stage('Docker Run') {
     steps{
         script {
             sshagent(credentials : ['upgrad']){
                sh 'ssh -o StrictHostKeyChecking=no ubuntu@10.0.2.121 "docker login -u AWS -p $(aws ecr get-login-password --region us-east-1) 402521938984.dkr.ecr.us-east-1.amazonaws.com/assignment && docker pull 402521938984.dkr.ecr.us-east-1.amazonaws.com/assignment:latest && docker stop $(docker ps -a -q --filter name=node) && docker run -d -p 8081:8081 --rm --name node 402521938984.dkr.ecr.us-east-1.amazonaws.com/assignment"'

             }
                
                
            }
      }
    }
    }
}