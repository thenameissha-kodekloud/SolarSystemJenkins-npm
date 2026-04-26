pipeline {
    agent any

    tools {
    nodejs 'nodejs-22-6-0'
    }
    stages {
        stage('VM Node version and npm version') {
            steps {
                '''
                bat node -v
                bat npm -v
                bat npm install --no-audit
                '''
            }
        }
    }
}