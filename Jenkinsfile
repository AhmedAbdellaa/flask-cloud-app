pipeline {
    agent any
    environment {
        IMAGE = "docker.io/ahmed123465746/flask-app-image"
        TAG = "${BUILD_NUMBER}"
    }
    
    stages {
        stage('build') {
            steps {
                sh 'docker build -t "$IMAGE:$TAG" -t "$IMAGE:latest" .'
            }
        }
        stage('push') {
            steps {
                    withCredentials([usernamePassword(credentialsId: 'CI-CD-Docker', passwordVariable: 'DOCKER_UP', usernameVariable: 'DOCKER_UN')]) {
                    sh 'echo "$DOCKER_UP" | docker login -u "$DOCKER_UN" --password-stdin'
                    sh 'docker push "$IMAGE:$TAG"'
                    sh 'docker push "$IMAGE:latest"'
                }
            }
        }
        stage('deploy') {
            steps {
                sh 'docker pull "$IMAGE:$TAG"'
                sh 'docker rm -f flask-app || true'
                sh 'docker run -d --name flask-app -p 5000:5000 "$IMAGE:$TAG"'
            }
        }
        stage('run') {
            steps {
                echo 'hit http://localhost:5000 to enter the web page'
            }
        }
    }
}
