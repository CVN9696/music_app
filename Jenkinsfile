pipeline {
    agent any

    environment {
        IMAGE_NAME = "vamsichamarthi/music-app"
        TAG = "${BUILD_NUMBER}"
        DOCKER_CRED = "docker_Cred"
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
                echo "Building artifact..."
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
                withDockerRegistry([credentialsId: DOCKER_CRED, url: '']) {
                    sh '''
                    docker push $IMAGE_NAME:$TAG
                    docker push $IMAGE_NAME:latest
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                echo "Updating deployment..."

                # Delete old deployment (optional)
                kubectl delete deployment music-app --ignore-not-found=true

                # Apply new deployment with updated image
                sed "s|IMAGE_TAG|$TAG|g" k8s/deployment.yaml | kubectl apply -f -

                kubectl apply -f k8s/service.yaml
                '''
            }
        }
    }

}
