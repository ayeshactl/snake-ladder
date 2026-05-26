pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                echo "📥 Cloning repository..."
                checkout scm
            }
        }

       stage('Build Docker Image') {
    steps {
        echo "🐳 Building Docker image..."
        sh """
            docker build -t snake-game:${BUILD_NUMBER} .
            docker tag snake-game:${BUILD_NUMBER} snake-game:latest
        """
    }
}
        stage('Run Container') {
            steps {
                echo "🚀 Deploying container..."
                sh '''
                    docker stop snake || true
                    docker rm snake || true
                    docker run -d -p 8081:80 --name snake snake-game:latest
                '''
            }
        }

        stage('Smoke Test') {
            steps {
                echo "🧪 Testing application..."
                sh 'curl -f http://localhost:8081 || exit 1'
            }
        }

    }

    post {
        success {
            echo "✅ Pipeline SUCCESS - Game deployed!"
        }

        failure {
            echo "❌ Pipeline FAILED"
        }
    }
}