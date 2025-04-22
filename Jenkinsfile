pipeline{
    agent any
    environment{
        IMAGE_NAME = 'amar90224/my-images'
        IMAGE_TAG = '${IMAGE_NAME}:${env.BUILD_ID}'
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
                echo "$IMAGE_TAG"
                sh 'docker build -t ${IMAGE_TAG} .'
                sh 'docker push ${IMAGE_TAG}'
                echo 'Docker build and Push was succesful'
            }
        }

        stage('Deploy to dev'){
            steps{
                sh 'kubectl config user-context jenkins-deployer@dev.us-east-1.eksctl.io'
                sh 'kubectl config current-context'
                sh 'kubectl set image deployment/flask-app flask-app=${IMAGE_TAG} '

            }
        }

        stage('Deploy to Prod'){
            steps{
                sh 'kubectl config user-context jenkins-deployer@prod.us-east-1.eksctl.io'
                sh 'kubectl config current-context'
                sh 'kubectl set image deployment/flask-app flask-app=${IMAGE_TAG} '

            }
        }
    }
}