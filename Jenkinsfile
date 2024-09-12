pipeline {
  agent any
  stages {
    node {

      
      stage('Git Checkout') {
        steps {
          git 'https://github.com/Ujjwaldotcom/Project-CICD.git'
        }
      }

      stage('Sending Dockerfile to Ansible server over ssh') {
        steps {
          sshagent(['ansible']) {
            sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.42.89'
            sh 'scp /var/lib/jenkins/workspace/Project-CICD/* ubuntu@172.31.42.89:/home/ubuntu'
          }
        }
      }

      stage('Docker Image Building') {
        steps {
          sshagent(['ansible']) {
            sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.42.89 cd /home/ubuntu/'
            sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.42.89 docker image build -t $JOB_NAME:v1.$BUILD_ID .'
          }
        }
      }


      
    }
  }
}
