pipeline {
    agent any

    environment {
        IMAGE_NAME = "vamsichamarthi/music-app"
        TAG = "latest"
    }

    stages {

        stage('Clone Code') {
            steps {
                git 'https://github.com/CVN9696/music_app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$TAG .'
            }
        }

        stage('Push Image') {
            steps {
                withDockerRegistry([credentialsId: 'docker-cred', url: '']) {
                    sh 'docker push $IMAGE_NAME:$TAG'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                kubectl apply -f deployment.yaml
                kubectl apply -f service.yaml
                kubectl rollout restart deployment music-app
                '''
            }
        }
    }

    post {
        success {
            mail to: 'your-email@gmail.com',
                 subject: "SUCCESS: Music App Deployed",
                 body: "Deployment completed successfully 🚀"
        }
        failure {
            mail to: 'your-email@gmail.com',
                 subject: "FAILED: Deployment Failed",
                 body: "Pipeline failed ❌"
        }
    }
}
