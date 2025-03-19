pipeline {
    agent any

    environment {
        GITHUB_TOKEN = credentials('GITHUB_TOKEN')  // Fetch GitHub Token from Jenkins
        GCP_CREDENTIALS = credentials('gcp-service-account-key')  // Fetch GCP Service Account Key
        GCP_PROJECT_ID = 'regal-hybrid-454111-r7'  // Your GCP Project ID
        ARTIFACT_REGISTRY = 'asia-south1-docker.pkg.dev/regal-hybrid-454111-r7/docker-images'
        IMAGE_NAME = 'register-app'
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Checkout Code') {
            steps {
                script {
                    sh "git clone https://${GITHUB_TOKEN}@github.com/ankush152002/register-app.git"
                    dir("source-code") {
                        sh "git checkout main"
                    }
                }
            }
        }

        stage('Authenticate with GCP') {
            steps {
                script {
                    sh """
                        echo '${GCP_CREDENTIALS}' > gcp-key.json
                        gcloud auth activate-service-account --key-file=gcp-key.json
                        gcloud auth configure-docker asia-south1-docker.pkg.dev --quiet
                    """
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
