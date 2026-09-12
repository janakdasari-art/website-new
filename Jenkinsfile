pipeline {
    agent any

    triggers {
        githubPush()
    }

    environment {
        IMAGE_NAME = "website-new"
        CONTAINER_NAME = "website-container"
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
                    docker build -t $IMAGE_NAME:latest .
                '''
            }
        }

        stage('Deploy Latest Container') {
            steps {
                sh '''
                    docker stop $CONTAINER_NAME || true
                    docker rm $CONTAINER_NAME || true

                    docker run -d \
                      --name $CONTAINER_NAME \
                      -p 80:80 \
                      $IMAGE_NAME:latest
                '''
            }
        }
    }
}
