pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                checkout scm
                sh 'cp .env.example .env'
                sh 'docker compose up -d'
                sh 'docker compose run --rm composer install'
                sh 'sleep 10 && docker compose run --rm artisan key:generate'
                sh 'docker compose run --rm artisan migrate'
            }
        }
        stage('Test') {
            steps {
                sh 'docker compose run --rm artisan test'
            }
        }
    }
    post { 
        always { 
            echo 'I will always say Hello again!'
        }
    }
}