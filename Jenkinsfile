pipeline {
    agent any

    environment {
        // Your Docker Hub repository
        IMAGE = "sohaylahossam/ci-test"
        TAG = "${env.BUILD_NUMBER ?: 'latest'}"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh """
                        echo "=== Building Docker image ==="
                        docker build -t ${IMAGE}:${TAG} .
                    """
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    // Uses your Docker Hub access token stored in Jenkins
                    withCredentials([usernamePassword(
                        credentialsId: 'docker-hub-credentials',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_TOKEN'
                    )]) {
                        sh """
                            echo "=== Logging into Docker Hub ==="
                            echo $DOCKER_TOKEN | docker login -u $DOCKER_USER --password-stdin

                            echo "=== Pushing Docker image ==="
                            docker push ${IMAGE}:${TAG}
                        """
                    }
                }
            }
        }
    }

    post {
        always {
            echo "Cleaning workspace…"
            cleanWs()
        }
    }
}
