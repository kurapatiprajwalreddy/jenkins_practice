pipeline {
    agent any

    environment {
        IMAGE_TAG = "v1.0"
        BRANCH_NAME = "${env.GIT_BRANCH}".split('/').last()
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: "${BRANCH_NAME}", url: 'git@github.com:Shivmangal-Singh/cicd-pipeline.git'
            }
        }

        stage('Build') {
            steps {
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    def imageName = (BRANCH_NAME == 'main') ? "nodemain:${IMAGE_TAG}" : "nodedev:${IMAGE_TAG}"
                    sh "docker build -t ${imageName} ."
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    def imageName = (BRANCH_NAME == 'main') ? "nodemain:${IMAGE_TAG}" : "nodedev:${IMAGE_TAG}"
                    def port = (BRANCH_NAME == 'main') ? "3000" : "3001"

                    // Clean previous container for this branch
                    sh """
                        docker ps -q --filter "ancestor=${imageName}" | xargs -r docker stop
                        docker ps -aq --filter "ancestor=${imageName}" | xargs -r docker rm
                        docker run -d -p ${port}:3000 ${imageName}
                    """
                }
            }
        }
    }
}
