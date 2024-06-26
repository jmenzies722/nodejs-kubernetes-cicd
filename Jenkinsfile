pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'ci-cd-node-app'
        REGISTRY = 'jmenzies722'
    }
    stages {
        stage('Checkout') {
            steps {
                git(
                    url: 'https://github.com/jmenzies722/nodejs-kubernetes-cicd.git',
                    credentialsId: 'gh_credentials',
                    branch: 'main'
                )
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${DOCKER_IMAGE}:${env.BUILD_ID}")
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'docker_cicd') {
                        dockerImage.push('latest')
                        dockerImage.push("${env.BUILD_ID}")
                    }
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    withKubeConfig([credentialsId: 'kubeconfig']) {
                        sh 'kubectl apply -f k8s/deployment.yaml'
                    }
                }
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}
