pipeline {
    agent any

    environment {
        IMAGE_NAME = "krishna100radha/boutique-app:${BUILD_NUMBER}"
    }

    stages {

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Login & Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    docker push $IMAGE_NAME
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh """
                kubectl set image deployment/boutique-app boutique=$IMAGE_NAME
                """
            }
        }
    }

    post {
        success {
            echo '✅ Build, Push & Deployment Successful!'
        }
        failure {
            echo '❌ Pipeline Failed. Check logs.'
        }
    }
}