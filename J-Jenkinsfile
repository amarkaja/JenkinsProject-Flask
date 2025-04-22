pipeline{
    agent any

    environment{
        SERVER_IP = '52.87.159.1'
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
                    sh "scp -i $MY_SSH_KEY -o StrictHostKeyChecking=no myapp.zip ${username}@${SERVER_IP}:/home/ec2-user/"
                    sh """
                    ssh -i $MY_SSH_KEY -o StrictHostKeyChecking=no ${username}@${SERVER_IP} << 
                    EOF
                        unzip -o /home/ec2-user/myapp.zip -d /home/ec2-user/pythonflask/
                        source pythonflask/venv/bin/activate
                        cd /home/ec2-user/pythonflask/
                        sudo pip install -r requirements.txt
                        sudo systemctl restart flaskapp.service
EOF
                    """
                }      
            }
        }
    }
}            