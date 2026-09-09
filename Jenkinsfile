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
                    bat 'npm install'
                    echo 'Backend dependencies installed successfully'
                }
            }
        }

        stage('Install Frontend Dependencies') {
            steps {
                dir('frontend') {
                    bat 'npm install'
                    echo 'Frontend dependencies installed successfully'
                }
            }
        }

        stage('Environment Setup') {
            steps {
                dir('backend') {
                    script {
                        bat '''
                            if not exist .env (
                                copy .env.example .env
                                echo Environment file copied from .env.example
                            ) else (
                                echo .env file already exists, skipping copy
                            )
                        '''
                    }
                    echo 'Environment file prepared'
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    bat "${DOCKER_COMPOSE} build"
                    echo 'Docker images built successfully'
                }
            }
        }

        stage('Start Containers') {
            steps {
                script {
                    bat "${DOCKER_COMPOSE} up -d"
                    echo 'Containers started successfully'
                }
            }
        }

        stage('Health Check') {
            steps {
                script {
                    sleep 10
                    bat "${DOCKER_COMPOSE} ps"
                    echo 'Health check completed'
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
            script {
                try {
                    bat "${DOCKER_COMPOSE} logs --tail=50"
                } catch (Exception e) {
                    echo "Could not fetch docker logs: ${e.getMessage()}"
                }
            }
        }
        failure {
            echo 'Pipeline failed!'
            script {
                try {
                    bat "${DOCKER_COMPOSE} logs --tail=100"
                } catch (Exception e) {
                    echo "Could not fetch docker logs: ${e.getMessage()}"
                }
            }
        }
        always {
            echo 'Cleaning up workspace...'
        }
    }
}
