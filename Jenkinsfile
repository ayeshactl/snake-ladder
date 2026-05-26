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
            docker stop snake-normal || true
            docker rm snake-normal || true

            docker stop snake-fast || true
            docker rm snake-fast || true

            docker run -d -p 8081:80 \
                --name snake \
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
    stage('Create Build Report') {
    steps {
        sh """
            echo "Build Number: ${BUILD_NUMBER}" > build-info.txt
            echo "Game Mode: ${params.GAME_MODE}" >> build-info.txt
            echo "Docker Image: snake-game:${BUILD_NUMBER}" >> build-info.txt
        """
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