pipeline {
    agent any

    parameters {
        choice(
            name: 'GAME_MODE',
            choices: ['normal', 'fast'],
            description: 'Select game mode'
        )
    }

    stages {

        stage('Checkout Code') {
            steps {
                echo "📥 Pulling latest code from GitHub..."
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

        stage('Cleanup Old Images') {
            steps {
                echo "🧹 Cleaning unused Docker images..."

                sh """
                    docker image prune -f
                """
            }
        }

        stage('Health Check') {
            steps {
                echo "🔍 Running application health check..."

                sh """
                    echo "Health check stage started"
                """
            }
        }

       stage('Run Container') {
    when {
        branch 'master'
    }

    steps {
        echo "🚀 Deploying latest container..."

        sh """
            docker rm -f snake || true

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
                    sleep 5
                    curl -f http://localhost:8081 || exit 1
                """
            }
        }

        stage('Create Build Report') {
            steps {
                echo "📄 Creating build artifact..."

                sh """
                    echo "Build Number: ${BUILD_NUMBER}" > build-info.txt
                    echo "Game Mode: ${params.GAME_MODE}" >> build-info.txt
                    echo "Branch: ${BRANCH_NAME}" >> build-info.txt
                    echo "Docker Image: snake-game:${BUILD_NUMBER}" >> build-info.txt
                    echo "Status: SUCCESS" >> build-info.txt
                """
            }
        }
    }

    post {

        success {
            echo "✅ Pipeline SUCCESS - Snake Game deployed!"

            archiveArtifacts artifacts: 'build-info.txt', fingerprint: true
        }

        failure {
            echo "❌ Pipeline FAILED - Check logs!"
        }

        always {
            echo "📊 Build completed: ${BUILD_NUMBER}"
        }
    }
}