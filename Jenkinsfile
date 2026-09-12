pipeline {
    agent any

    triggers {
        githubPush()
    }

    environment {
        DOCKER_IMAGE = "YOUR_DOCKERHUB_USERNAME/website-new:latest"
        CONTAINER_NAME = "website-container"
        APP_SERVER = "10.0.2.144"
    }

    stages {

        stage('Pull Code from GitHub') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/janakdasari-art/website-new.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build -t $DOCKER_IMAGE .
                '''
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                        docker push $DOCKER_IMAGE
                        docker logout
                    '''
                }
            }
        }

        stage('Deploy to Application Server') {
            steps {
                sshagent(['app-server-ssh']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no ubuntu@$APP_SERVER "
                            docker pull $DOCKER_IMAGE &&
                            docker stop $CONTAINER_NAME || true
                            docker rm $CONTAINER_NAME || true
                            docker run -d \
                                --name $CONTAINER_NAME \
                                --restart unless-stopped \
                                -p 80:80 \
                                $DOCKER_IMAGE
                        "
                    '''
                }
            }
        }
    }
}
