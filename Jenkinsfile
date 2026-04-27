pipeline {
    agent any

    tools {
        nodejs 'nodejs-22-6-0'
    }

    environment {
        MONGO_URI      = "mongodb+srv://supercluster.d83jj.mongodb.net/superData"
        MONGO_DB_CREDS = credentials('mongo-db-credentials')
    }

    stages {
        stage('VM Node version and npm version') {
            steps {
                bat 'node -v'
                bat 'npm -v'
                bat 'npm install --no-audit'
            }
        }

        stage('Unit Test') {
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    bat 'npm test || exit 0'
                    bat 'echo colon separated- %MONGO_DB_CREDS%'       // ✅ Windows uses %VAR%
                    bat 'echo username - %MONGO_DB_CREDS_USR%'         // ✅ Windows uses %VAR%
                    bat 'echo password - %MONGO_DB_CREDS_PSW%'         // ✅ Windows uses %VAR%
                }
            }
            post {
                always {
                    junit allowEmptyResults: true, testResults: 'test-results.xml'
                }
            }
        }

        stage('Dependency Scanning') {
            parallel {
                stage('NPM Dependencies Audit') {
                    steps {
                        bat 'npm audit --audit-level=critical || exit 0'
                    }
                }
                stage('OWASP Dependency Check') {
                    steps {
                        dependencyCheck additionalArguments: '--scan ./ --out ./ --format ALL --prettyPrint --noupdate',
                            odcInstallation: 'OWASP-DepCheck-10'
                    }
                }
            }
        }

        stage('Publish OWASP Report') {
            steps {
                publishHTML([
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: './',
                    reportFiles: 'dependency-check-jenkins.html',
                    reportName: 'OWASP Report',
                    reportTitles: ''
                ])
            }
            post {
                always {
                    junit allowEmptyResults: true, testResults: 'dependency-check-junit.xml'
                }
            }
        }
    }
}