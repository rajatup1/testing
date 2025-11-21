pipeline {
    agent any

    environment {
        DOCKERHUB_CREDS_ID = 'dockerhub-cred'
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

        stage('Run docker app') {
            steps {
                sh '''
                    docker run --rm -p 3000:3000 rajat9893308210/simple-node-app:latest
                '''
            }
        }

    } // end stages
}
