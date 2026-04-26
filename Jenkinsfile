pipeline {
    agent any

    tools {
        nodejs 'nodejs-22-6-0'
    }

    stages {
        stage('VM Node version and npm version') {
            steps {
                bat 'node -v'
                bat 'npm -v'
                bat 'npm install --no-audit'
            }
        }

        stage('Dependency Scanning') {
            parallel {

                stage('NPM Dependencies Audit') {
                    steps {
                        bat 'npm audit --audit-level=critical'
                        bat 'echo %ERRORLEVEL%'
                    }
                }

                stage('OWASP Dependency Check') {
                    steps {
                        dependencyCheck additionalArguments: '--scan ./ --out ./ --format ALL --prettyPrint --noupdate', odcInstallation: 'OWASP-DepCheck-10'
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

                post {
                always {
                    junit allowEmptyResults: true, testResults: 'dependency-check-junit.xml'
                }
            }
        }

    }
}