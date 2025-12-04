pipeline {
    agent any

    environment {
        IMAGE_TAG = "v${BUILD_NUMBER}"
        DOCKER_REGISTRY = 'docker.io/bachukeshav07'
        // This uses the Jenkins credentials ID you created for your PAT
        REGISTRY_CREDENTIALS = credentials('dockerhub-creds')
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/BachuKeshav/example-voting-app.git'
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    def services = ['vote', 'worker', 'result']
                    services.each { service ->
                        sh """
                        echo "Building Docker image for ${service}..."
                        docker build -t ${DOCKER_REGISTRY}/${service}:${IMAGE_TAG} ./${service}
                        """
                    }
                }
            }
        }

        stage('Push Images to Docker Hub') {
            steps {
                script {
                    sh """
                    # Login to Docker Hub using PAT stored in Jenkins
                    echo ${REGISTRY_CREDENTIALS_PSW} | docker login -u ${REGISTRY_CREDENTIALS_USR} --password-stdin ${DOCKER_REGISTRY}

                    # Push all service images
                    docker push ${DOCKER_REGISTRY}/vote:${IMAGE_TAG}
                    docker push ${DOCKER_REGISTRY}/worker:${IMAGE_TAG}
                    docker push ${DOCKER_REGISTRY}/result:${IMAGE_TAG}
                    """
                }
            }
        }
    }

    post {
        always {
            echo "CI pipeline finished for build #${BUILD_NUMBER}"
        }
    }
}
#####