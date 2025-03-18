pipeline {
    agent any

    environment {
        PROJECT_ID = 'regal-hybrid-454111-r7'  // Your GCP Project ID
        REGION = 'asia-south1'
        REPO_NAME = 'docker-images'  // Your Artifact Registry repository name
        IMAGE_NAME = 'asia-south1-docker.pkg.dev/${PROJECT_ID}/${REPO_NAME}/tomcat-webapp:latest'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/ankush152002/register-app.git'  // Replace with your actual GitHub repo URL
            }
        }

        stage('Authenticate with GCP') {
            steps {
                withCredentials([file(credentialsId: 'gcp-service-account-key', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
                    sh 'gcloud auth activate-service-account --key-file=$GOOGLE_APPLICATION_CREDENTIALS'
                    sh 'gcloud config set project $PROJECT_ID'
                    sh 'gcloud auth configure-docker asia-south1-docker.pkg.dev --quiet'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'  // No need to specify Dockerfile path
            }
        }

        stage('Push Docker Image to Artifact Registry') {
            steps {
                sh 'docker push $IMAGE_NAME'
            }
        }
    }

    post {
        success {
            echo "✅ Image pushed successfully: $IMAGE_NAME"
        }
        failure {
            echo "❌ Failed to push image!"
        }
    }
}
