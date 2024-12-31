pipeline {
    agent any
    environment {
        PYTHON_PATH = 'C:\\Program Files\\Python311;C:\\Program Files\\Python311\\Scripts'
    }
    stages {
        stage('Checkout') {
            steps { 
                checkout scm
            }
        }

        stage('Verify Coverage Installation') {
            steps {
                bat '''
                set PATH=%PYTHON_PATH%;%PATH%
                pip install coverage || exit /b 1
                pip show coverage
                '''
            }
        }

        stage('Run Unit Tests and Generate Coverage') {
            steps {
                bat '''
                set PATH=%PYTHON_PATH%;%PATH%
                echo Running tests with coverage...
                coverage run --source=. test_myapp.py || exit /b 1
                coverage xml -o coverage.xml || exit /b 1
                if exist coverage.xml (
                    echo Coverage report generated successfully.
                ) else (
                    echo Error: Coverage report not found!
                    exit /b 1
                )
                '''
            }
        }

        stage('Ensure Correct Working Directory') {
            steps {
                bat '''
                set PATH=%PYTHON_PATH%;%PATH%
                echo Current working directory: %cd%
                dir
                '''
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withCredentials([string(credentialsId: 'new_coverage', variable: 'SONAR_TOKEN')]) {
                    bat '''
                    set PATH=C:\\Users\\Admin\\Downloads\\scanner\\sonar-scanner-6.2.1.4610-windows-x64\\bin;%PATH%
                    sonar-scanner.bat -D"sonar.projectKey=new_coverage" -D"sonar.sources=." -D"sonar.host.url=http://localhost:9000" -D"sonar.token=%SONAR_TOKEN%"
                    '''
                }
            }
        }
    }
    post {
        success {
            echo 'Pipeline completed successfully'
        }
        failure {
            echo 'Pipeline failed'
        }
        always {
            echo 'This runs regardless of the result.'
        }
    }
}
