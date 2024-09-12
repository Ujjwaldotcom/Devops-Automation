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

      stage('Docker Tag Image') {
        steps {
          sshagent(['ansible']) {
            sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.42.89 cd /home/ubuntu/'
            sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.42.89 docker image tag $JOB_NAME:v1.$BUILD_ID ujjwaljagtiani/$JOB_NAME:v1.$BUILD_I .'
            sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.42.89 docker image tag $JOB_NAME:v1.$BUILD_ID ujjwaljagtiani/$JOB_NAME:latest '
          }
        }
      }

      stage('Push Docker Image to DockerHub') {
        steps {
          sshagent(['ansible']) {
            withCredentials([string(CredentialsId: 'dockerhub_passwd', variable: 'dockerhub_passwd')]) {
              sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.42.89 docker login -u ujjwaljagtiani -p ${dockerhub_passwd}"
              sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.42.89 docker image push ujjwaljagtiani/$JOB_NAME:v1.$BUILD_ID '
              sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.42.89 docker image push ujjwaljagtiani/$JOB_NAME:v1.$BUILD_ID '
            }
          }
        }
      }

      stage('Copy files from Ansible to Kubernetes') {
        steps {
          sshagent(['MyApp(K8s)']) {
              sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.42.89'
              sh 'scp /var/lib/jenkins/workspace/Project-CICD/* ubuntu@172.31.1.158:/home/ubuntu'
            }
        }
      }

      stage('Kubernetes deployment using ansible') {
        steps {
          sshagent(['ansible']) {
              sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.42.89'
              sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.42.89 ansible -m ping node
              sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.42.89 ansible-playbook ansible.yml'
            }
        }
      }
      
    }
  }
}
