pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'akshata26/kubernetes_web_app'  // Replace with your Docker Hub username and app name
        DOCKER_CREDENTIALS_ID = 'docker-hub-credentials'        // Docker credentials ID
        KUBECONFIG_CREDENTIALS_ID = 'kubeconfig-credentials'    // Kubeconfig credentials ID
        DEPLOYMENT_NAME = 'web-app-deployment'                  // Kubernetes deployment name
        KUBE_NAMESPACE = 'default'                               // Kubernetes namespace
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/akshata-thorkar/jenkins_kunernetes.git'  // Replace with your Git repo URL
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ${DOCKER_IMAGE}:${BUILD_NUMBER} .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('', "${DOCKER_CREDENTIALS_ID}") {
                        sh 'docker push ${DOCKER_IMAGE}:${BUILD_NUMBER}'
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([file(credentialsId: "${KUBECONFIG_CREDENTIALS_ID}", variable: 'KUBECONFIG')]) {
                    sh """
                    kubectl apply -f deployment.yaml   // Ensure this file is present in your repo
                    kubectl set image deployment/${DEPLOYMENT_NAME} ${DEPLOYMENT_NAME}=${DOCKER_IMAGE}:${BUILD_NUMBER} -n ${KUBE_NAMESPACE}
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful'
        }
        failure {
            echo 'Deployment Failed'
        }
    }
}
