pipeline {
    agent any

    // Настройка триггера для периодического опроса репозитория
    triggers {
        pollSCM('H/1 * * * *') 
    }

    stages {
        stage('Checkout') {
            steps {
                // Клонируем репозиторий
                checkout scm
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    // Сборка Docker-образа с уникальным тегом на основе build ID
                    def image = docker.build("rgr_image:${env.BUILD_ID}")
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Остановка и удаление старого контейнера (если существует)
                    sh 'docker stop rgr_container || true'
                    sh 'docker rm rgr_container || true'
                    
                    // Запуск нового контейнера с обновленным образом
                    sh "docker run -d --name rgr_container -p 8081:8080 rgr_image:${env.BUILD_ID}"
                }
            }
        }
    }

    post {
        always {
            script {
                // Очистка старых Docker-образов после завершения пайплайна
                sh 'docker image prune -f'
            }
        }
    }
}

