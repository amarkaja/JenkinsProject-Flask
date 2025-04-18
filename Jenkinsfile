pipeline{
    agent any

    environment{
        SERVER_IP = credentials('ec2-server-IP')
    }

    stages{
        stage('package the code'){
            steps{
                sh "zip -r myapp.zip ./* -x .git*"
                sh "ls -alrt"
            }
        }

        stage("DeploytoProd"){
            steps{
                withCredentials([sshUserPrivateKey(credentialsId: 'ec2-private-key', keyFileVariable: 'MY_SSH_KEY', usernameVariable: 'username')]){

                    sh "scp -i MY_SSH_KEY_SSH_KEY -o StrictHostKeyChecking=no myapp.zip ${username}@${SERVER_IP}:/home/ec2-user/"
                    sh "scp -i MY_SSH_KEY_SSH_KEY -o StrictHostKeyChecking=no flaskcommands.txt ${username}@${SERVER_IP}:/home/ec2-user/"
                    sh "bash flaskcommands.txt"
                }      
            }
        }
    }
}            