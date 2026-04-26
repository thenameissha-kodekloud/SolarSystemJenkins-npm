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

        stage('Dependency Scanning') {   // ← quotes required!
            parallel {

                stage('NPM Dependencies Audit') {
                    steps {
                        bat 'npm audit --audit-level=critical'
                        bat 'echo %ERRORLEVEL%'
                    }
                }

                stage('OWASP Dependency Check') {
                    steps {
                        dependencyCheck additionalArguments: '--scan ./ --out ./ --format ALL --prettyPrint', odcInstallation: 'OWASP-DepCheck-10'
                    }
                }

            }
        }

    }
}