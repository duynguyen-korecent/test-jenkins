pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                checkout scm
                sh 'pwd && cd src && /usr/local/bin/composer install'
                sh 'cp .env.example .env'
                sh 'cd src && /usr/local/bin/docker-compose up -d'
                sh 'cd src && /usr/local/bin/docker-compose run --rm composer install'
                sh 'sleep 10 && cd src && /usr/local/bin/docker-compose run --rm artisan key:generate'
                sh 'cd src && /usr/local/bin/docker-compose run --rm artisan migrate'
            }
        }
    }
}