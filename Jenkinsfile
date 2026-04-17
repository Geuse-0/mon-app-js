pipeline {
    agent any

    environment {
        DOCKER_HUB_USER = 'votre_username'
        IMAGE_NAME = 'mon-app-js'
        REGISTRY_CREDENTIALS_ID = 'docker-hub-credentials'
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
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
                    dockerImage = docker.build("${DOCKER_HUB_USER}/${IMAGE_NAME}:${env.BUILD_ID}")
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('', REGISTRY_CREDENTIALS_ID) {
                        dockerImage.push()
                        dockerImage.push('latest')
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f deployment.yaml'
                sh 'kubectl apply -f service.yaml'
            }
        }
    }

    post {
        success {
            mail to: 'admin@example.com',
                 subject: "Succès du Pipeline: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                 body: "Le déploiement s'est terminé avec succès."
        }
        failure {
            mail to: 'admin@example.com',
                 subject: "ÉCHEC du Pipeline: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                 body: "Veuillez vérifier les logs Jenkins."
        }
    }
}
