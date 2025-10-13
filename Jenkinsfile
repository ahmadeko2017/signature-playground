pipeline {
    agent { label 'ahmad-paylabs' }

    environment {
        IMAGE_NAME = "paylabs-signature-playground"
        IMAGE_TAG = "${BUILD_NUMBER}"
        COMPOSE_PATH = "docker/docker-compose.yml"
        CERT_PATH = "docker/certs"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    credentialsId: 'ahmad-gitlab',
                    url: 'https://gitlab.local/paylabs/api-improvement/signature-playgroung.git'
            }
        }

        stage('Generate Self-Signed Certificate') {
            steps {
                echo "🔐 Generating SSL certificate for sign.play ..."
                sh """
                    mkdir -p ${CERT_PATH}
                    HOST_IP=$(hostname -I | awk '{print $1}')
                    echo "Detected IP: \$HOST_IP"

                    openssl req -x509 -newkey rsa:2048 -nodes \
                        -keyout ${CERT_PATH}/sign.play-key.pem \
                        -out ${CERT_PATH}/sign.play.pem \
                        -subj "/CN=sign.play" \
                        -addext "subjectAltName=DNS:sign.play,IP:\$HOST_IP" \
                        -sha256 -days 365
                """
            }
        }

        stage('Build Image (with cert)') {
            steps {
                echo "🔧 Building Nginx image using self-signed cert..."
                sh """
                    podman build -f docker/Dockerfile -t ${IMAGE_NAME}:${IMAGE_TAG} .
                """
            }
        }

        stage('Deploy') {
            steps {
                echo "🚀 Deploying container..."
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
            echo "✅ HTTPS deployed at https://sign.play (or https://<your-IP>)"
        }
        failure {
            echo "❌ Build failed — check Podman logs."
        }
    }
}