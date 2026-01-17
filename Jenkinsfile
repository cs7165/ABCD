pipeline {
    agent any

    environment {
        IMAGE_NAME = "cs7165/abcdverse-website"
        IMAGE_TAG  = "latest"
        CONTAINER  = "abcdverse-container"
    }

    stages {

        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/cs7165/ABCD.git'
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                docker build -t abcdverse-app .
                '''
            }
        }

        stage('Tag & Push Docker Image') {
            steps {
                withDockerRegistry(credentialsId: 'docker-password') {
                    sh '''
                    docker tag abcdverse-app ${IMAGE_NAME}:${IMAGE_TAG}
                    docker push ${IMAGE_NAME}:${IMAGE_TAG}
                    '''
                }
            }
        }

        stage('Deploy Container on EC2') {
            steps {
                sh '''
                docker rm -f ${CONTAINER} || true

                docker run -d \
                --name ${CONTAINER} \
                -p 80:80 \
                ${IMAGE_NAME}:${IMAGE_TAG}
                '''
            }
        }
    }

    post {
        success {
            echo "✅ ABCDverse Website Deployed Successfully"
        }
        failure {
            echo "❌ ABCDverse Website Deployment Failed"
        }
    }
}

