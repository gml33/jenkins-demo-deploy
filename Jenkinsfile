pipeline {
    agent any

    environment {
        APP_NAME = 'demo-app2'
        IMAGE_BASE = 'demo-app2'
        STACK_FILE = 'stack.yml'
        REPO_URL = 'https://github.com/gml33/jenkins-demo-deploy.git'
        BRANCH = 'main'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: "${BRANCH}",
                    credentialsId: 'github-gml33',
                    url: "${REPO_URL}"
            }
        }

        stage('Debug') {
            steps {
                sh '''
                pwd
                git rev-parse --short HEAD
                cat public/index.html
                '''
            }
        }

        stage('Build image') {
            steps {
                sh '''
                export IMAGE_NAME="${IMAGE_BASE}:${BUILD_NUMBER}"
                docker build --no-cache -t "${IMAGE_NAME}" .
                '''
            }
        }

        stage('Deploy stack') {
            steps {
                sh '''
                export IMAGE_NAME="${IMAGE_BASE}:${BUILD_NUMBER}"
                docker stack deploy -c ${STACK_FILE} ${APP_NAME}
                '''
            }
        }

        stage('Verify') {
            steps {
                sh '''
                docker service inspect ${APP_NAME}_web --format '{{.Spec.TaskTemplate.ContainerSpec.Image}}'
                docker service ps ${APP_NAME}_web --no-trunc
                '''
            }
        }
    }
}