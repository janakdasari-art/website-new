pipeline {
    agent any

    triggers {
        githubPush()
    }

    environment {
        DOCKER_USERNAME = "janakdasari"
        DOCKER_TOKEN = "dckr_pat_v6QD_JshfDuCo22vGuo1dlKaSYo"
        DOCKER_IMAGE = "janakdasari/website-new:latest"
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
                sh '''
                    echo "$DOCKER_TOKEN" | docker login \
                        -u "$DOCKER_USERNAME" \
                        --password-stdin

                    docker push $DOCKER_IMAGE

                    docker logout
                '''
            }
        }

        stage('Deploy to Application Server') {
            steps {
                sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@$APP_SERVER "
                        docker pull $DOCKER_IMAGE
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
