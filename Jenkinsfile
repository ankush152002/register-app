pipeline {
    agent any

    environment {
        GCP_PROJECT_ID = 'regal-hybrid-454111-r7'  // Your GCP Project ID
        ARTIFACT_REGISTRY = 'asia-south1-docker.pkg.dev/regal-hybrid-454111-r7/docker-images'
        IMAGE_NAME = 'register-app'
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Checkout Code') {
            steps {
                script {
                    // Remove the directory if it exists to prevent conflicts
                    sh "rm -rf source-code"
                    
                    // Clone repo securely using GitHub Token
                    withCredentials([string(credentialsId: 'GITHUB_TOKEN', variable: 'GITHUB_TOKEN')]) {
                        sh "git clone https://$GITHUB_TOKEN@github.com/ankush152002/register-app.git source-code"
                    }

                    // Checkout the main branch
                    dir("source-code") {
                        sh "git checkout main"
                    }
                }
            }
        }

        stage('Authenticate with GCP') {
            steps {
                script {
                    withCredentials([file(credentialsId: 'gcp-service-account-key', variable: 'GCP_CREDENTIALS')]) {
                        sh """
                            gcloud auth activate-service-account --key-file=$GCP_CREDENTIALS
                            gcloud auth configure-docker asia-south1-docker.pkg.dev --quiet
                        """
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh """
                        cd source-code
                        docker build -t $ARTIFACT_REGISTRY/$IMAGE_NAME:$IMAGE_TAG .
                    """
                }
            }
        }

        stage('Push Docker Image to Artifact Registry') {
            steps {
                script {
                    sh "docker push $ARTIFACT_REGISTRY/$IMAGE_NAME:$IMAGE_TAG"
                }
            }
        }
    }

    post {
        success {
            echo "✅ Deployment Successful!"
        }
        failure {
            echo "❌ Deployment Failed!"
        }
    }
}
