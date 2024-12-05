pipeline {
    agent any

    parameters {
        string(name: 'GIT_BRANCH', defaultValue: 'main', description: 'Branch to build')
        string(name: 'DOCKER_IMAGE_TAG', defaultValue: 'latest', description: 'Docker image tag')
    }

    environment {
        DOCKER_IMAGE_NAME = 'ecommerce'
        DOCKER_CONTAINER_NAME = 'ecommerce_container'
        APP_PORT = '8000'
    }
    stages {
        stage('Clear Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout Code') {
            steps {
                git branch: params.GIT_BRANCH, changelog: false, poll: false, url: 'https://github.com/kumbharprath/FlexiCart.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh """
                        docker build -t ${DOCKER_IMAGE_NAME}:${params.DOCKER_IMAGE_TAG} .
                    """
                }
            }
        }

        stage('Clean Up Old Containers') {
            steps {
                script {
                    sh """
                        docker rm -f ${DOCKER_CONTAINER_NAME} || true
                    """
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    sh """
                        docker run -d --name ${DOCKER_CONTAINER_NAME} -p ${APP_PORT}:${APP_PORT} ${DOCKER_IMAGE_NAME}:${params.DOCKER_IMAGE_TAG}
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed! Please check the logs.'
            mail to: 'team@example.com',
                 subject: "Jenkins Build Failed: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                 body: "Check the Jenkins logs for job: ${env.BUILD_URL}"
        }
    }
}
