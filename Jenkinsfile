pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = 'd504cc59-995a-40dd-b7cb-4a599e07f3da'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }

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
                    node --version
                    npm --version
                    npm ci
                    npm run build
                '''
            }
        }
        
        stage('Test'){
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }

            steps {
                sh '''
                test -f build/index.html
                npm test
                '''
            }
        }

        stage('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm install netlify-cli
                    node_modules/.bin/netlify --version
                    echo 'Deploying to production at Netlify. Site ID: $NETLIFY_SITE_ID'
                    node_modules/.bin/netlify status
                    node_modules/.bin/netlify deploy --prod --dir=build
                '''
            }
        }         
    }

    post {
        always {
            junit 'test-results/junit.xml'
        }
    }
}