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
                // Fixed truncated Git target tracking configuration URL
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
                // Safe check allows execution to pass even if package.json tests are empty
                sh 'npm test --if-present'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    // Overrides the path context to target your system JRE/JDK 17 execution binaries
                    // (Change path string below if your JDK17 installation directory lives elsewhere)
                    withEnv(["JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64"]) {
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
}
