pipeline {

    agent any

    environment {
        IMAGE = "adarsh206/blogging-app"
        APP_SERVER = "10.0.2.144"
    }

    stages {

        stage('Clone Repository') {
            steps {
                git branch: 'master', url: 'https://github.com/adarshsharma206/project.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE}:latest ."
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    '''
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh "docker push ${IMAGE}:latest"
            }
        }

        stage('Deploy') {
            steps {
                sh """
                ssh -o StrictHostKeyChecking=no ubuntu@${APP_SERVER} '
                sudo docker pull ${IMAGE}:latest &&
                sudo docker stop blogging || true &&
                sudo docker rm blogging || true &&
                sudo docker run -d --name blogging -p 9090:80 ${IMAGE}:latest
                '
                """
            }
        }

    }

    post {
        success {
            echo 'Application deployed successfully.'
        }

        failure {
            echo 'Pipeline failed.'
        }
    }
}
