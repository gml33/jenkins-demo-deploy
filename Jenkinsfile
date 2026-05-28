pipeline {
    agent any

    environment {
        APP_NAME = 'demo-app2'
        IMAGE_NAME = 'demo-app2:latest'
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

        stage('Workspace debug') {
            steps {
                sh '''
                pwd
                cat public/index.html
                '''
            }
        }

        stage('Build image') {
            steps {
                sh '''
                docker build --no-cache -t ${IMAGE_NAME} .
                '''
            }
        }

        stage('Deploy stack') {
            steps {
                sh '''
                docker stack deploy -c ${STACK_FILE} ${APP_NAME}
                docker service update --force ${APP_NAME}_web
                '''
            }
        }

        stage('Verify') {
            steps {
                sh '''
                docker service inspect ${APP_NAME}_web --format '{{.Spec.TaskTemplate.ContainerSpec.Image}}'
                docker service ps ${APP_NAME}_web
                '''
            }
        }
    }

    post {
        success {
            echo 'Deploy completado correctamente: https://cachilo.ensigna.tech'
        }

        failure {
            echo 'Deploy fallido.'
        }
    }
}