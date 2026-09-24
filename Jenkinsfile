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

                sh '''
                cat > deploy-info-$BUILD_NUMBER.txt <<EOF
                build: $BUILD_NUMBER
                image: $IMAGE:$TAG
                commit: ${GIT_COMMIT}
                branch: $GIT_BRANCH
                time: $(date -u +"%Y-%m-%dT%H:%M:%SZ")
                url: $BUILD_URL
                EOF
                '''
             archiveArtifacts artifacts: "deploy-info-${BUILD_NUMBER}.txt", fingerprint: true

            }
        }
        stage('run') {
            steps {
                echo 'hit http://localhost:5000 to enter the web page'
            }
        }
        stage('cleanup') {
      steps {
        cleanWs()
      }
    }
    }
post {
    success {echo "Build ${env.BUILD_NUMBER} succeeded"}
    failure{echo "Build ${env.BUILD_NUMBER} failed"}
    alwys{echo "Build ${env.BUILD_NUMBER} finished"}
}
}
