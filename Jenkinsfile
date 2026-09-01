pipeline {
    agent any

    tools {
        nodejs 'nodejs'
        sonarScanner 'sonar-scanner'
    }

    environment {
        CI = 'true'
    }

    stages {
        stage('Checkout') {
            steps {
                // Fixed the clipped repository URL
                git branch: 'main', url: 'https://github.com/Leeo800/nodejs-project-31082026-snoarqube.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                echo 'Installing project dependencies...'
                sh 'npm ci'
            }
        }

        stage('Run Linting & Tests') {
            steps {
                echo 'Running unit tests with coverage tracking...'
                sh 'npm test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    echo 'Starting SonarQube scan with coverage reporting...'
                    // Removed the trailing '>' typo from the exclusions configuration
                    sh '''
                        sonar-scanner \
                        -Dsonar.projectKey=nodejs-project \
                        -Dsonar.sources=. \
                        -Dsonar.exclusions=**/node_modules/**,**/*.spec.js,**/*.test.js \
                        -Dsonar.tests=. \
                        -Dsonar.test.inclusions=**/*.spec.js,**/*.test.js \
                        -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info
                    '''
                }
            }
        }
    }
}
