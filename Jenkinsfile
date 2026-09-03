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
                sh 'npm test --if-present'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
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

        // NEW: Enforces Quality Gate metrics before marking the build as a success
        stage("Quality Gate Check") {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    script {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            error "Pipeline aborted due to SonarQube Quality Gate failure: ${qg.status}"
                        }
                    }
                }
            }
        }
    }

    // NEW: Keeps your Jenkins agent disk space clean after execution completes
    post {
        always {
            cleanWs()
        }
    }
}
