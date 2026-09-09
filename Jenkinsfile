pipeline {
    agent any

    environment {
        DOCKER_COMPOSE = 'docker-compose'
        APP_NAME = 'food-waste-management'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
                echo 'Checked out source code successfully'
            }
        }

        stage('Install Backend Dependencies') {
            steps {
                dir('backend') {
                    sh 'npm install'
                    echo 'Backend dependencies installed successfully'
                }
            }
        }

        stage('Install Frontend Dependencies') {
            steps {
                dir('frontend') {
                    sh 'npm install'
                    echo 'Frontend dependencies installed successfully'
                }
            }
        }

        stage('Environment Setup') {
            steps {
                dir('backend') {
                    sh 'cp .env.example .env || true'
                    echo 'Environment file prepared'
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    sh "${DOCKER_COMPOSE} build"
                    echo 'Docker images built successfully'
                }
            }
        }

        stage('Start Containers') {
            steps {
                script {
                    sh "${DOCKER_COMPOSE} up -d"
                    echo 'Containers started successfully'
                }
            }
        }

        stage('Health Check') {
            steps {
                script {
                    sleep 10
                    sh "${DOCKER_COMPOSE} ps"
                    echo 'Health check completed'
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
            sh "${DOCKER_COMPOSE} logs --tail=50 || true"
        }
        failure {
            echo 'Pipeline failed!'
            sh "${DOCKER_COMPOSE} logs --tail=100 || true"
        }
        always {
            echo 'Cleaning up workspace...'
        }
    }
}
