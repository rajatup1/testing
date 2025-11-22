pipeline {
    agent any

    environment {
        DOCKERHUB_CREDS_ID = 'dockerhub-cred'
        EC2_USER = 'ec2-user'
        EC2_HOST = '15.206.73.38'
        SSH_KEY =  credentials('ec2-ssh-key')
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scmGit(
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[url: 'https://github.com/rajatup1/testing.git']]
                )
            }
        }

        stage('Build docker image') {
            steps {
                sh '''
                    docker build -t rajat9893308210/simple-node-app:latest .
                '''
            }
        }

        stage('Login to DockerHub') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: env.DOCKERHUB_CREDS_ID,
                        usernameVariable: 'USER',
                        passwordVariable: 'PASS'
                    )
                ]) {
                    sh '''
                        echo $PASS | docker login -u $USER --password-stdin
                    '''
                }
            }
        }

        stage('Push Image to Dockerhub') {
            steps {
                sh '''
                    docker push rajat9893308210/simple-node-app:latest
                '''
            }
        }

        stage('Deploy to EC2') {
            steps {
                sh '''
                    ssh -i $SSH_KEY $EC2_USER@$EC2_HOST '
                    docker pull rajat9893308210/simple-node-app:latest &&
                    docker stop simple-node-app || true &&
                    docker rm simple-node-app || true &&
                    docker run -d -p 3000:3000 --name simple-node-app rajat9893308210/simple-node-app:latest
                  '
                '''
            }
        }

    } // end stages
}
