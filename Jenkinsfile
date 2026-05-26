pipeline {
    agent any

    // 🎮 Build Parameter
    parameters {
        choice(name: 'GAME_MODE', choices: ['normal', 'fast'], description: 'Select game mode')
    }

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
                echo "🎮 Mode selected: ${params.GAME_MODE}"

                sh """
                    docker build -t snake-game:${BUILD_NUMBER} .
                    docker tag snake-game:${BUILD_NUMBER} snake-game:latest
                """
            }
        }

        stage('Run Container') {
            steps {
                echo "🚀 Deploying container..."

                sh """
                    docker stop snake || true
                    docker rm snake || true

                    docker run -d -p 8081:80 \
                        --name snake-${params.GAME_MODE} \
                        snake-game:${BUILD_NUMBER}
                """
            }
        }

        stage('Smoke Test') {
            steps {
                echo "🧪 Running smoke test..."

                sh """
                    sleep 3
                    curl -f http://localhost:8081 || exit 1
                """
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline SUCCESS - Snake Game deployed!"
        }

        failure {
            echo "❌ Pipeline FAILED - Check logs!"
        }

        always {
            echo "📊 Build completed: ${BUILD_NUMBER}"
        }
    }
}