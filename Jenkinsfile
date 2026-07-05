pipeline {

    agent any

    environment {
        IMAGE = "adarsh206/blogging-app"
    }

    stages {

        stage('Clone Repository') {
            steps {
                git 'https://github.com/adarshsharma206/blogging-app.git'
            }
        }

        stage('Build Image') {
            steps {
                sh 'docker build -t $IMAGE:latest .'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'USERNAME',
                    passwordVariable: 'PASSWORD'
                )]) {
                    sh 'echo $PASSWORD | docker login -u $USERNAME --password-stdin'
                }
            }
        }

        stage('Push Image') {
            steps {
                sh 'docker push $IMAGE:latest'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                ssh ubuntu@10.0.2.144 << EOF
                docker pull $IMAGE:latest
                docker stop blogging || true
                docker rm blogging || true
                docker run -d --name blogging -p 80:80 $IMAGE:latest
                EOF
                '''
            }
        }
    }
}
