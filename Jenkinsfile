pipeline {
    agent any

    parameters {
        choice(name: 'GAME_MODE', choices: ['normal', 'fast'], description: 'Select game mode')
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                    docker build -t snake-game:${BUILD_NUMBER} .
                    docker tag snake-game:${BUILD_NUMBER} snake-game:latest
                """
            }
        }

        stage('Run Container') {
            steps {
                sh """
                    docker stop snake || true
                    docker rm snake || true

                    docker run -d -p 8081:80 --name snake snake-game:${BUILD_NUMBER}
                """
            }
        }

        stage('Smoke Test') {
            steps {
                sh "curl -f http://localhost:8081 || exit 1"
            }
        }

        stage('Create Build Report') {
            steps {
                sh """
                    echo "Build Number: ${BUILD_NUMBER}" > build-info.txt
                    echo "Game Mode: ${params.GAME_MODE}" >> build-info.txt
                    echo "Image: snake-game:${BUILD_NUMBER}" >> build-info.txt
                """
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline SUCCESS"
            archiveArtifacts artifacts: 'build-info.txt', fingerprint: true
        }

        failure {
            echo "❌ Pipeline FAILED"
        }

        always {
            echo "Build finished: ${BUILD_NUMBER}"
        }
    }
}