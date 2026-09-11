pipeline {
    agent any

    environment {
        PATH = "/opt/homebrew/bin:${env.PATH}"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/rxv801/8.2CDevSecOps.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install --legacy-peer-deps || true'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'npm test || true'
            }
        }

        stage('Generate Coverage Report') {
            steps {
                sh 'npm run coverage || true'
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                sh 'npm audit || true'
            }
        }

        stage('SonarCloud Analysis') {
            steps {
                withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
                    sh '''
                        SCANNER=sonar-scanner-7.3.0.5189-macosx-aarch64
                        if [ ! -d "$SCANNER" ]; then
                            curl -sSLo scanner.zip "https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/${SCANNER}.zip"
                            unzip -q -o scanner.zip
                        fi
                        "$SCANNER/bin/sonar-scanner" -Dsonar.token="$SONAR_TOKEN"
                    '''
                }
            }
        }
    }
}
