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
                git branch: 'main', url: 'https://github.com/Leeo800/nodejs-project-31082026.git'
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
                sh 'npm test --if-present'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    echo 'Starting SonarQube scan with coverage reporting...'
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
