pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub-credentials')
    }

    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/yourusername/simple-flask-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("yourdockerhubusername/simple-flask-app:${env.BUILD_ID}")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'DOCKER_HUB_CREDENTIALS') {
                        dockerImage.push()
                    }
                }
            }
        }

        stage('Deploy to AWS EC2') {
            steps {
                sshagent(['aws-ec2-key']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no ec2-user@your-ec2-public-ip '
                        docker pull yourdockerhubusername/simple-flask-app:${BUILD_ID} &&
                        docker stop simple-flask-app || true &&
                        docker rm simple-flask-app || true &&
                        docker run -d -p 5000:5000 --name simple-flask-app yourdockerhubusername/simple-flask-app:${BUILD_ID}'
                    '''
                }
            }
        }
    }
}
