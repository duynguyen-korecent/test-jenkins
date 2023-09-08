pipeline {
    agent any
    environment {
        // Github Repository
        // Telegram Message Pre Build
        CURRENT_BUILD_NUMBER = "${currentBuild.number}"
        // GIT_MESSAGE = sh(returnStdout: true, script: "git log -n 1 --format=%s ${GIT_COMMIT}").trim()
        // GIT_AUTHOR = sh(returnStdout: true, script: "git log -n 1 --format=%ae ${GIT_COMMIT}").trim()
        // GIT_COMMIT_SHORT = sh(returnStdout: true, script: "git rev-parse --short ${GIT_COMMIT}").trim()
        GIT_INFO = "Branch(Version): ${GIT_BRANCH}\nPull Request: ${PR_TITLE} #${PR_ID}"
        TEXT_BREAK = "--------------------------------------------------------------"
        TEXT_PRE_BUILD = "${TEXT_BREAK}\n${GIT_INFO}\n${JOB_NAME} is Building"

        // Telegram Message Success and Failure
        TEXT_SUCCESS_BUILD = "${JOB_NAME} is Success"
        TEXT_FAILURE_BUILD = "${JOB_NAME} is Failure"
    }
    stages {
        stage('Pre-Build') {
            steps {
                withCredentials([string(credentialsId: 'telegramToken', variable: 'TOKEN'), string(credentialsId: 'telegramChatid', variable: 'CHAT_ID')]) {
                    sh 'curl --location --request POST "https://api.telegram.org/bot${TOKEN}/sendMessage" --form text="${TEXT_PRE_BUILD}" --form chat_id="${CHAT_ID}"'
                }
            }
        }
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
        stage('End') {
            steps {
                sh 'docker compose down -v'
            }
        }
    }
    post {
        success {
            script{
                withCredentials([string(credentialsId: 'telegramToken', variable: 'TOKEN'), string(credentialsId: 'telegramChatid', variable: 'CHAT_ID')]) {
                    sh 'curl --location --request POST "https://api.telegram.org/bot${TOKEN}/sendMessage" --form text="${TEXT_SUCCESS_BUILD}" --form chat_id="${CHAT_ID}"'
                }
            }
        }
        failure {
            script{
                withCredentials([string(credentialsId: 'telegramToken', variable: 'TOKEN'), string(credentialsId: 'telegramChatid', variable: 'CHAT_ID')]) {
                    sh 'curl --location --request POST "https://api.telegram.org/bot${TOKEN}/sendMessage" --form text="${TEXT_FAILURE_BUILD}" --form chat_id="${CHAT_ID}"'
                }
            }
        }
    }
}

