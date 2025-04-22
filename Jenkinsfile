pipeline{
    agent any
    environment{
        IMAGE_NAME = 'amar90224/my-images'
        KUBECONFIG = credentials('kubeconfig')
        AWS_ACCESS_KEY_ID = credentials('aws-access-key')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-key')

    }

    stages{
        stage('DockerHUB Login'){
            steps{
                withCredentials([usernamePassword(credentialsId: 'dockercreds', passwordVariable: 'password', usernameVariable: 'username')]) {
                   sh '''

                   echo "$password" |docker login -u $username --password-stdin

                   '''
                }
            }
        }

        stage('Build and Push the Image'){
            steps{
                //echo "env.$IMAGE_TAG"
                sh "docker build -t ${env.IMAGE_NAME}:${env.BUILD_ID} ."
                sh "docker push ${env.IMAGE_NAME}:${env.BUILD_ID}" 
                echo "Docker build and Push was succesful"
            }
        }

        stage('Deploy to dev'){
            steps{
                sh 'ls -la $KUBECONFIG'
                sh 'chmod 644 $KUBECONFIG'
                sh 'ls -la $KUBECONFIG'
                sh 'kubectl config use-context jenkins-deployer@dev.us-east-1.eksctl.io'
                sh 'kubectl config current-context'
                sh "kubectl set image deployment/flask-app flask-app=${env.IMAGE_NAME}:${env.BUILD_ID}"

            }
        }

        stage('Deploy to Prod'){
            steps{
                sh 'kubectl config use-context jenkins-deployer@prod.us-east-1.eksctl.io'
                sh 'kubectl config current-context'
                sh "kubectl set image deployment/flask-app flask-app=${env.IMAGE_NAME}:${env.BUILD_ID}"

            }
        }
    }
}