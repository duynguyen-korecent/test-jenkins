pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                git branch: 'main', credentialsId: 'ssh-github', url: 'git@github.com:duynguyen-korecent/test-jenkins.git/'
                sh 'composer install'
                sh 'cp .env.example .env'
                sh 'php artisan key:generate'
            }
        }
        stage('Test') {
            steps {
                sh './vendor/bin/phpunit'
            }
        }
    }
}