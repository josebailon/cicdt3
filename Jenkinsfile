
pipeline {
    agent any
    environment {
        REMOTE_HOST = 'localhost'
        DOCKER_IMAGE = 'ensayos-server:latest'
        NOMBRE = 'contenedor-server'
    }
 stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code from GitHub...'
                git url: 'https://github.com/josebailon/cicdt3.git', branch: 'master'
            }
        }
        
        stage('Test'){
            steps {
                echo 'Testing the project with Maven inside Docker...'
                script {
                    docker.image('maven:3.8.3-openjdk-17').inside {
                        sh 'mvn test'
                    }
                }
            }
            
            post {
                always{
                    junit '**/target/surefire-reports/TEST-*.xml'
                }
            }
        }

        stage('Build') {
            steps {
                echo 'Building the project with Maven inside Docker...'
                script {
                    docker.image('maven:3.8.3-openjdk-17').inside {
                        sh 'mvn clean package'
                    }
                }
            }
        }

        stage('Deploy to Remote Server') {
            steps {
                echo 'Deploying Docker container to remote server..'
                echo REMOTE_HOST
                echo 'Antes'
                echo 'REMOTE_SSH_CREDENTIALS_ID'
                echo env.DOCKER_IMAGE
                sshagent(['REMOTE_SSH_CREDENTIALS_ID']) {
                    sh """
                    ssh -o StrictHostKeyChecking=no ubuntu@${REMOTE_HOST} '
                        docker-compose down
                        docker-compose up --build
                    '
                    """
                }
                echo 'despues'
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed'
        }
    }
}
