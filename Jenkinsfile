pipeline {
    agent any

    environment {
        BACKEND_IMAGE  = 'smart-food-waste-backend'
        FRONTEND_IMAGE = 'smart-food-waste-frontend'
        DOCKER         = 'C:\\Program Files\\Docker\\Docker\\resources\\bin\\docker.exe'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
                echo 'Source code checked out successfully.'
            }
        }

        stage('Check Node.js') {
            steps {
                bat 'node --version'
                bat 'npm --version'
                echo 'Node.js and npm are available.'
            }
        }

        stage('Install Backend Dependencies') {
            steps {
                dir('backend') {
                    bat 'npm install'
                    echo 'Backend dependencies installed successfully.'
                }
            }
        }

        stage('Install Frontend Dependencies') {
            steps {
                dir('frontend') {
                    bat 'npm install --legacy-peer-deps'
                    echo 'Frontend dependencies installed successfully.'
                }
            }
        }

        stage('Lint Frontend') {
            steps {
                dir('frontend') {
                    bat 'npx eslint . || exit 0'
                    echo 'Frontend lint completed.'
                }
            }
        }

        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    bat 'npm run build'
                    echo 'Frontend build completed successfully.'
                }
            }
        }

        stage('Check Docker') {
            steps {
                bat '"%DOCKER%" --version'
                bat '"%DOCKER%" compose version'
                echo 'Docker and Docker Compose are available.'
            }
        }

        stage('Build Docker Images') {
            steps {
                bat '"%DOCKER%" build -t %BACKEND_IMAGE%:%BUILD_NUMBER% ./backend'
                bat '"%DOCKER%" build -t %FRONTEND_IMAGE%:%BUILD_NUMBER% ./frontend'
                echo 'Docker images built successfully.'
            }
        }

        stage('Deploy') {
            steps {
                bat '"%DOCKER%" compose down'
                bat '"%DOCKER%" compose up -d --build'
                echo 'Application deployed successfully.'
            }
        }

        stage('Verify Deployment') {
            steps {
                bat '"%DOCKER%" compose ps'
                echo 'Deployment verification completed.'
            }
        }
    }

    post {
        success {
            echo '=============================================='
            echo 'SMART FOOD WASTE MANAGEMENT SYSTEM'
            echo 'BUILD AND DEPLOYMENT SUCCESSFUL!'
            echo '=============================================='
        }

        failure {
            echo '=============================================='
            echo 'SMART FOOD WASTE MANAGEMENT SYSTEM'
            echo 'BUILD FAILED - CHECK THE LOGS ABOVE.'
            echo '=============================================='
        }

        always {
            echo 'Jenkins pipeline execution finished.'
        }
    }
}
