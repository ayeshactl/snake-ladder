pipeline {
    agent any

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
        sh """
            docker build -t snake-game:${BUILD_NUMBER} .
            docker tag snake-game:${BUILD_NUMBER} snake-game:latest
        """
           echo "🎮 Running in mode: ${params.GAME_MODE}"
    }
}
    stage('Run Container') {
    steps {
        echo "🚀 Deploying container..."
        sh """
            docker stop snake || true
            docker rm snake || true
            docker run -d -p 8081:80 --name snake snake-game:${BUILD_NUMBER}
            docker run -d -p 8081:80 --name snake-${params.GAME_MODE} snake-game:${BUILD_NUMBER}
           """
        
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