pipeline {
    agent any
    
    stages('Git checkout'){
            steps{
                git url: 'https://github.com/Mahesh8887/project-4.git', branch: 'main' 
            }
        }
        stage('Sending docker file to Ansible server over ssh'){
            sshagent(['ansible_demo']) {
            steps{
                sh 'ssh -o $trictHostKeyChecking ubuntu@172.31.86.246'
                sh 'scp /var/lib/jenkins/workspace/pipeline-demo/* ubuntu@172.31.86.246:/home/ubuntu/'
            }
        }
        }
         stage('Docker Build image'){
            sshagent(['ansible_demo']) {
            steps{
                sh 'ssh -o $trictHostKeyChecking ubuntu@172.31.86.246 cd /home/ubuntu/'
                sh 'ssh -o $trictHostKeyChecking ubuntu@172.31.86.246 docker image build -t $JOB_NAME:v1.$BUILD_ID '
            }
        }
        }
         stage('Docker image tagging'){
            sshagent(['ansible_demo']) {
            steps{
                sh 'ssh -o $trictHostKeyChecking ubuntu@172.31.86.246 cd /home/ubuntu/'
                sh 'ssh -o $trictHostKeyChecking ubuntu@172.31.86.246 docker image build -t $JOB_NAME:v1.$BUILD_ID mahesh8887/$JOB_NAME:v1.$BUILD_ID'
                sh 'ssh -o $trictHostKeyChecking ubuntu@172.31.86.246 docker image build -t $JOB_NAME:v1.$BUILD_ID mahesh8887/$JOB_NAME:v1.$BUILD_ID'
		                                                                 docker build tag $JOB_NAME:v1.$BUILD_ID mahesh8887/$JOB_NAME:v1.$BUILD_ID
            }
        }
        }   
         stage('Push docker image to docker hub'){
            sshagent(['ansible_demo']) {
            steps{
                withCredentials([usernamePassword(credentialsId: 'dockerHub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
                 sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
                 sh 'ssh -o $trictHostKeyChecking ubuntu@172.31.86.246 docker image push mahesh8887/$JOB_NAME:v1.$BUILD_ID mahesh8887/$JOB_NAME:v1.$BUILD_ID'
                 sh 'ssh -o $trictHostKeyChecking ubuntu@172.31.86.246 docker image push mahesh8887/$JOB_NAME:latest'
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
