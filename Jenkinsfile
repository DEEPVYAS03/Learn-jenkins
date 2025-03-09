pipeline {
    agent any

    stages {
        stage('w/o docker') {
            steps {
                sh '''
                    echo "Without docker"
                    ls -la
                    touch container-no.txt
                '''
            }
        }

        stage('w/ docker') {
            agent {
                docker {
                    image 'node:18-alpine'
                }
            }
            steps {
                sh '''
                    echo "Before starting container"
                    docker ps -a  # Check all containers before running
                    
                    echo "With docker"
                    ls -la

                    echo "After running container"
                    docker ps  # Check running containers
                '''
            }
        }

        stage('After docker') {
            steps {
                sh '''
                    echo "After docker stage"
                    docker ps -a  # Check if the container still exists
                '''
            }
        }
    }
}
