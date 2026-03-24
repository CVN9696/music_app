pipeline {
    agent any

    environment {
        IMAGE_NAME = "vamsichamarthi/music-app"
        TAG = "${BUILD_NUMBER}"
    }


    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/CVN9696/music_app.git'
            }
        }

        stage('Build Artifact') {
            steps {
                sh '''
                mkdir -p target
                cp index.html target/
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t $IMAGE_NAME:$TAG .
                docker tag $IMAGE_NAME:$TAG $IMAGE_NAME:latest
                '''
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker_Cred', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    docker push $IMAGE_NAME:$TAG
                    docker push $IMAGE_NAME:latest
                    docker logout
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                kubectl apply -f k8s/deployment.yaml
                kubectl apply -f k8s/service.yaml
                kubectl set image deployment/music-app music-app=$IMAGE_NAME:$TAG
                kubectl rollout status deployment/music-app
                '''
            }
        }
    }
}
