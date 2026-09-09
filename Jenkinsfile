pipeline {
agent any

```
environment {
    DOCKER = 'C:\\Program Files\\Docker\\Docker\\resources\\bin\\docker.exe'
}

stages {

    stage('Checkout') {
        steps {
            checkout scm
            echo 'Source code checked out successfully'
        }
    }

    stage('Check Docker') {
        steps {
            bat "\"%DOCKER%\" --version"
            bat "\"%DOCKER%\" compose version"
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
                            echo .env created successfully
                        ) else (
                            echo ERROR: .env.example not found
                            exit /b 1
                        )
                    ) else (
                        echo .env already exists
                    )
                '''
            }
        }
    }

    stage('Check Docker Compose File') {
        steps {
            bat 'dir'
            bat 'dir docker-compose.yml'
            echo 'Docker Compose file found'
        }
    }

    stage('Build Docker Images') {
        steps {
            bat "\"%DOCKER%\" compose build"
            echo 'Docker images built successfully'
        }
    }

    stage('Start Containers') {
        steps {
            bat "\"%DOCKER%\" compose up -d"
            echo 'Containers started successfully'
        }
    }

    stage('Health Check') {
        steps {
            bat 'timeout /t 10 /nobreak'
            bat "\"%DOCKER%\" compose ps"
            echo 'Health check completed'
        }
    }
}

post {
    success {
        echo '======================================'
        echo 'PIPELINE COMPLETED SUCCESSFULLY!'
        echo 'SMART FOOD WASTE MANAGEMENT SYSTEM'
        echo '======================================'
        bat "\"%DOCKER%\" compose ps"
    }

    failure {
        echo '======================================'
        echo 'PIPELINE FAILED'
        echo '======================================'
        bat "\"%DOCKER%\" compose ps"
    }

    always {
        echo 'Jenkins pipeline execution finished.'
    }
}
```

}
