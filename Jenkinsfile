
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
        
        /*stage('Test'){
                agent {
                    docker {
                        image 'maven:3.9.0-eclipse-temurin-19-alpine'
                        args '-u root'
                        reuseNode true
                    }
                }
            steps {
                echo 'Testing the project with Maven inside Docker...'
                script {
                        sh 'mvn test'
                }
            }
            
            post {
                always{
                    junit '**/target/surefire-reports/TEST-*.xml'
                }
            }
        }*/

        stage('Build') {
                agent {
                    docker {
                        image 'maven:3.9.0-eclipse-temurin-19-alpine'
                        args '-u root'
                        reuseNode true
                    }
                }
            steps {
                echo 'Building the project with Maven inside Docker...'
                script {
                        sh 'mvn clean package'
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
                        cd /var/lib/jenkins/workspace/T3
                        ls -l
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
