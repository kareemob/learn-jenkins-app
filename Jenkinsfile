pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    ls -la
                    cat build/index.html
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
                '''
            }
        }

        stage('Test') { 
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo 'Running tests...'
                    npm test
                '''
            }
        }

        stage('e2e tests') { 
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.50.1-noble'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm install serve
                    node_modules/.bin/serve -s build &
                    sleep 15
                    npx playwright test
                '''
            }
        }
    }

    post {
        always {
            script {
                try {
                    junit 'jest-results/junit.xml'
                } catch (Exception e) {
                    echo 'No test results found, skipping JUnit report.'
                }
            }
        }
    }
}