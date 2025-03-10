pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = 'fc910c37-8e1f-4285-8ca8-525949c83587'
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

        stage('Tests') {
            parallel {
                stage('Unit tests') {
                    agent {
                        docker {
                            image 'node:18-alpine'
                            reuseNode true
                        }
                    }

                    steps {
                        sh '''
                            #test -f build/index.html
                            npm test
                        '''
                    }
                    post {
                        always {
                            sh 'ls -R test-results'
                            junit 'test-results/junit.xml'
                        }
                    }
                }

                // stage('E2E') {
                //     agent {
                //         docker {
                //             image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                //             reuseNode true
                //         }
                //     }

                //     steps {
                //         sh '''
                //             npm install serve
                //             node_modules/.bin/serve -s build &
                //             sleep 10
                //             npx playwright test --reporter=junit,html --output=test-results

                //         '''
                //     }

                //     post {
                //         always {
                //             sh 'ls -R test-results'
                //             junit 'test-results/junit.xml'
                //             publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                //         }
                //     }
                // }
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
                    echo "Deploy to Netlify,SITE ID is $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status
                    node_modules/.bin/netlify deploy --dir=build --prod
                '''
            }
        }
    }
}