pipeline {
    agent any

    tools {
        nodejs 'nodejs-22-6-0'
    }

    environment {
        MONGO_URI         = "mongodb+srv://supercluster.d83jj.mongodb.net/superData"
        MONGO_DB_CREDS    = credentials('mongo-db-credentials')
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

        stage('Build Docker Image') {
            steps {
                bat "docker build -t sharanv/sharanjenkins:%GIT_COMMIT% ."
            }
        }

        stage('Trivy Vulnerability Scanner') {
            steps {
                bat """
                    trivy image sharanv/sharanjenkins:%GIT_COMMIT% ^
                        --severity LOW,MEDIUM ^
                        --exit-code 0 ^
                        --quiet ^
                        --format json -o trivy-image-MEDIUM-results.json

                    trivy image sharanv/sharanjenkins:%GIT_COMMIT% ^
                        --severity HIGH,CRITICAL ^
                        --exit-code 1 ^
                        --quiet ^
                        --format json -o trivy-image-CRITICAL-results.json
                """
            }
            post {
                always {
                    catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                        bat """
                            trivy convert ^
                                --format template --template "@C:\\Users\\Sharan\\AppData\\Local\\Microsoft\\WinGet\\Packages\\AquaSecurity.Trivy_Microsoft.Winget.Source_8wekyb3d8bbwe\\contrib\\html.tpl" ^
                                --output trivy-image-MEDIUM-results.html trivy-image-MEDIUM-results.json

                            trivy convert ^
                                --format template --template "@C:\\Users\\Sharan\\AppData\\Local\\Microsoft\\WinGet\\Packages\\AquaSecurity.Trivy_Microsoft.Winget.Source_8wekyb3d8bbwe\\contrib\\html.tpl" ^
                                --output trivy-image-CRITICAL-results.html trivy-image-CRITICAL-results.json

                            trivy convert ^
                                --format template --template "@C:\\Users\\Sharan\\AppData\\Local\\Microsoft\\WinGet\\Packages\\AquaSecurity.Trivy_Microsoft.Winget.Source_8wekyb3d8bbwe\\contrib\\junit.tpl" ^
                                --output trivy-image-MEDIUM-results.xml trivy-image-MEDIUM-results.json

                            trivy convert ^
                                --format template --template "@C:\\Users\\Sharan\\AppData\\Local\\Microsoft\\WinGet\\Packages\\AquaSecurity.Trivy_Microsoft.Winget.Source_8wekyb3d8bbwe\\contrib\\junit.tpl" ^
                                --output trivy-image-CRITICAL-results.xml trivy-image-CRITICAL-results.json
                        """
                    }

                    junit allowEmptyResults: true, testResults: 'trivy-image-CRITICAL-results.xml'
                    junit allowEmptyResults: true, testResults: 'trivy-image-MEDIUM-results.xml'
                }
            }
        }

    }

}