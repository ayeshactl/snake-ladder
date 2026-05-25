pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'master', url: 'https://github.com/ayeshactl/snake-ladder.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t snake-game .'
            }
        }

        stage('Stop Old Container') {
            steps {
                sh 'docker stop snake || true'
                sh 'docker rm snake || true'
            }
        }

        stage('Run New Container') {
            steps {
                sh 'docker run -d -p 8081:80 --name snake snake-game'
            }
        }
    }

    post {
        success {
            echo "🎮 Snake Game deployed successfully!"
        }

        failure {
            echo "❌ Build failed. Check logs."
        }
    }
}