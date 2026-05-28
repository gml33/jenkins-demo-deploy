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
                ls -lah
                find . -maxdepth 3 -type f | sort
                '''
            }
        }

        stage('Build image') {
            steps {
                sh '''
                docker build -t ${IMAGE_NAME} .
                '''
            }
        }

        stage('Deploy stack') {
            steps {
                sh '''
                docker stack deploy -c ${STACK_FILE} ${APP_NAME}
                '''
            }
        }

        stage('Verify') {
            steps {
                sh '''
                docker service ls | grep ${APP_NAME}
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
            echo 'Deploy fallido. Revisar logs del pipeline y estado del servicio Swarm.'
        }
    }
}