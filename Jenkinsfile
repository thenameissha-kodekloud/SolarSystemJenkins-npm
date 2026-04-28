pipeline {
    agent any

    tools {
        nodejs 'nodejs-22-6-0'
    }

    environment {
        MONGO_URI      = "mongodb+srv://supercluster.d83jj.mongodb.net/superData"
        MONGO_DB_CREDS = credentials('mongo-db-credentials')
        MONGO_DB_USERNAME = credentials('mongo-db-username')
        MONGO_DB_PASSWORD = credentials('mongo-db-password')
    }

    stages {
        stage('VM Node version and npm version') {
            steps {
                bat 'node -v'
                bat 'npm -v'
                bat 'npm install --no-audit'
            }
        }
        stage('Build Docker Image'){
            steps{
                bat 'docker build -t sharanv/sharanjenkins:$GIT_COMMIT'
            }
        }

    }
}
//hai