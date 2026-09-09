```groovy
pipeline {
    agent any

    environment {
        DOCKER_COMPOSE = 'docker compose'
        APP_NAME = 'food-waste-management'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
                echo 'Checked out source code successfully'
            }
        }

        stage('Check Docker') {
            steps {
                bat 'docker --version'
                bat 'docker compose version'
                echo 'Docker and Docker Compose are available'
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
                    bat '''
                        if not exist .env (
                            if exist .env.example (
                                copy .env.example .env
                                echo Environment file copied from .env.example
                            ) else (
                                echo ERROR: .env.example file not found
                                exit /b 1
                            )
                        ) else (
                            echo .env file already exists, skipping copy
                        )
                    '''
                    echo 'Environment file prepared'
                }
            }
        }

        stage('Check Docker Compose File') {
            steps {
                bat '''
                    if exist docker-compose.yml (
                        echo Found docker-compose.yml
                    ) else if exist docker-compose.yaml (
                        echo Found docker-compose.yaml
                    ) else if exist compose.yml (
                        echo Found compose.yml
                    ) else if exist compose.yaml (
                        echo Found compose.yaml
                    ) else (
                        echo ERROR: Docker Compose file not found
                        exit /b 1
                    )
                '''
                echo 'Docker Compose file checked'
            }
        }

        stage('Build Docker Images') {
            steps {
                bat 'docker compose build'
                echo 'Docker images built successfully'
            }
        }

        stage('Start Containers') {
            steps {
                bat 'docker compose up -d'
                echo 'Containers started successfully'
            }
        }

        stage('Health Check') {
            steps {
                bat 'timeout /t 10 /nobreak'
                bat 'docker compose ps'
                echo 'Health check completed'
            }
        }
    }

    post {

        success {
            echo 'Pipeline completed successfully!'

            script {
                try {
                    bat 'docker compose ps'
                    bat 'docker compose logs --tail=50'
                } catch (Exception e) {
                    echo "Could not fetch Docker logs: ${e.getMessage()}"
                }
            }
        }

        failure {
            echo 'Pipeline failed!'

            script {
                try {
                    bat 'docker compose ps'
                    bat 'docker compose logs --tail=100'
                } catch (Exception e) {
                    echo "Could not fetch Docker logs: ${e.getMessage()}"
                }
            }
        }

        always {
            echo 'Pipeline execution finished.'
        }
    }
}
```
