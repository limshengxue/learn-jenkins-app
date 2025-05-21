pipeline {
    agent any

    environment{
        NETLIFY_SITE_ID = 'aa790363-7d3d-4507-9680-49e384bbf9ab'
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
                    ls -la
                '''
            }
        }
        stage('Test'){
            agent{
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps{
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
                    npm install netlify-cli --save-dev
                    ./node_modules/.bin/netlify --version
                    echo "Deploying to production. Site ID: ${NETLIFY_SITE_ID}"
                    ./node_modules/.bin/netlify deploy --dir=build --prod --no-build=true
                '''
            }
        }
    }

    post{
        always{
            junit 'test-results/junit.xml'
        }
    }
}