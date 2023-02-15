pipeline {
    agent any
    
    stages{
        stage('Git Code checkout'){
            steps{
                git branch: 'main', url: 'https://github.com/Mahesh8887/project-kubernates.git' 
            }
        }
        stage('Sending docker file to Ansible server over ssh'){
            steps{
                sshagent(['jenkin_ansible']) {
                    sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.35.31'
                    sh "scp /var/lib/jenkins/workspace/project-1/* ubuntu@172.31.35.31:/home/ubuntu/"
                }
            }
        }
        stage('Docker Build image'){
            steps{
                sshagent(['jenkin_ansible']) {
                    sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.35.31 cd /home/ubuntu/'
                    sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.35.31 docker build -t $JOB_NAME:v1.$BUILD_ID .'
                }
            }
        }
        stage('Docker image tagging'){
            steps{
                sshagent(['jenkin_ansible']) {
                    sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.35.31 cd /home/ubuntu/'
                    sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.35.31 docker image tag $JOB_NAME:v1.$BUILD_ID mahesh8887/$JOB_NAME:v1.$BUILD_ID '
                    sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.35.31 docker image tag $JOB_NAME:v1.$BUILD_ID mahesh8887/$JOB_NAME:latest '
                }
            }
        }
        stage('Push docker image to docker hub'){
            steps{
                sshagent(['jenkin_ansible']) {
                    withCredentials([string(credentialsId: 'dockerhub_login', variable: 'dockerhub_login')]) {
                        sh "docker login -u mahesh8887 -p ${env.dockerhub_login}"
                        sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.35.31 docker image push mahesh8887/$JOB_NAME:v1.$BUILD_ID'
                    }
                }
            }
        }
        stage('Copy file from Ansible to Kubernetes server'){
		    sshagent(['kubernetes_server']){
                sh 'ssh -o $trictHostKeyChecking ubuntu@172.31.18.6'
                sh 'ssh -o $trictHostKeyChecking ubuntu@172.31.18.6 cd /home/ubuntu/'				
            }
        }
    }
}
