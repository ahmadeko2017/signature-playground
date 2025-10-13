pipeline {
    agent { label 'ahmad-paylabs' }

    environment {
        IMAGE_NAME = "paylabs-signature-playground"
        IMAGE_TAG = "${BUILD_NUMBER}"
        COMPOSE_PATH = "docker/docker-compose.yml"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    credentialsId: 'ahmad-gitlab',
                    url: 'https://gitlab.local/paylabs/api-improvement/signature-playgroung.git'
            }
        }

        stage('Build Image (Self-signed HTTPS)') {
            steps {
                echo "🔧 Building secure Nginx image..."
                sh """
                    podman build -f docker/Dockerfile -t ${IMAGE_NAME}:${IMAGE_TAG} .
                """
            }
        }

        stage('Deploy') {
            steps {
                echo "🚀 Deploying container with HTTPS..."
                dir('docker') {
                    sh """
                        podman-compose down || true
                        podman-compose -f ${COMPOSE_PATH} up -d
                    """
                }
            }
        }
    }

    post {
        success {
            echo "✅ HTTPS deployed at https://sign.play"
        }
        failure {
            echo "❌ Build failed — check Podman logs."
        }
    }
}