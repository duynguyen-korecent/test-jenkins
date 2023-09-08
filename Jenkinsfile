pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                checkout scm
                sh 'cp .env.example .env'
                sh '/usr/local/bin/docker-compose up -d'
                sh '/usr/local/bin/docker-compose run --rm composer install'
                sh 'sleep 10 && /usr/local/bin/docker-compose run --rm artisan key:generate'
                sh '/usr/local/bin/docker-compose run --rm artisan migrate'
            }
        }
    }
}