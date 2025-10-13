pipeline {
    agent { label 'ahmad-paylabs' }

    environment {
        GIT_SSL_NO_VERIFY = 'true'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    credentialsId: 'ahmad-gitlab',
                    url: 'https://gitlab.local/paylabs/api-improvement/signature-playgroung.git'
            }
        }

        stage('Build') {
            steps {
                echo 'Building application...'
                sh 'docker build -t paylabs-signature-playground .'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying to Podman...'
                sh '''
                podman stop paylabs-signature-playground || true
                podman rm paylabs-signature-playground || true
                podman run -d --name paylabs-signature-playground -p 8080:80 paylabs-signature-playground
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Deployment complete.'
        }
        failure {
            echo '❌ Build failed.'
        }
    }
}