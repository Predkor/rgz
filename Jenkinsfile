pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    def image = docker.build("rgr_image:${env.BUILD_ID}")
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    sh 'docker stop rgr_container || true'
                    sh 'docker rm rgr_container || true'
                    sh "docker run -d --name rgr_container -p 8081:8080 rgr_image:${env.BUILD_ID}"
                }
            }
        }
    }
    post {
        always {
            script {
                sh 'docker image prune -f'
            }
        }
    }
}
