pipeline {
    agent any
    parameters {
        string(name: 'PR_TITLE', defaultValue: '', description: 'Pull Request Title')
        string(name: 'PR_ID', defaultValue: 'NULL', description: 'Pull Request ID')
        string(name: 'ACTION', defaultValue: '', description: 'Pull Request Action')
    }
    triggers {
        GenericTrigger (
            genericVariables: [
                [key: 'PR_TITLE', value: '$.pull_request.title'],
                [key: 'PR_ID', value: '$.pull_request.number'],
                [key: 'ACTION', value: '$.action']
            ],
            causeString: 'Triggered by $PR_TITLE #$PR_ID',
            printContributedVariables: true,
            printPostContent: true,
            regexpFilterText: '$',
            regexpFilterExpression: '$',
            token: 'grave_app'
        )
    }
    environment {
        // Github Repository
        // Telegram Message Pre Build
        CURRENT_BUILD_NUMBER = "${currentBuild.number}"
        // GIT_MESSAGE = sh(returnStdout: true, script: "git log -n 1 --format=%s ${GIT_COMMIT}").trim()
        // GIT_AUTHOR = sh(returnStdout: true, script: "git log -n 1 --format=%ae ${GIT_COMMIT}").trim()
        // GIT_COMMIT_SHORT = sh(returnStdout: true, script: "git rev-parse --short ${GIT_COMMIT}").trim()
        GIT_INFO = "Branch(Version): ${GIT_BRANCH}\nPull Request: ${PR_TITLE} #${PR_ID}"
        // TEXT_PRE_BUILD = "${TEXT_BREAK}\n${GIT_INFO}\n${JOB_NAME} is Building"

        // Telegram Message Success and Failure
        // TEXT_SUCCESS_BUILD = "${JOB_NAME} is Success"
        // TEXT_FAILURE_BUILD = "${JOB_NAME} is Failure"
        TEXT_FORMAT= """
        Repository: ${GIT_URL}
        Mergre: <b>${GIT_BRANCH}</b> to: <b>${GIT_BRANCH}</b>
        Pull Request: <b>${PR_TITLE}</b> #${PR_ID}
        Build: <b>${CURRENT_BUILD_NUMBER}</b>
        """
        TEXT_SUCCESS_BUILD = """
        ${TEXT_FORMAT}
        Status: <b>Success</b>
        """
        TEXT_FAILURE_BUILD = """
        ${TEXT_FORMAT}
        Status: <b>Failure</b>
        """
    }
    stages {
        // stage('Pre-Build') {
        //     steps {
        //         withCredentials([string(credentialsId: 'telegramToken', variable: 'TOKEN'), string(credentialsId: 'telegramChatid', variable: 'CHAT_ID')]) {
        //             sh 'curl --location --request POST "https://api.telegram.org/bot${TOKEN}/sendMessage" --form text="${TEXT_PRE_BUILD}" --form chat_id="${CHAT_ID}"'
        //         }
        //     }
        // }
        stage('Build & Test') {
            when {
                expression {
                    return ACTION == 'opened' || ACTION == 'synchronize'
                }
            }
            steps {
                checkout scm
                sh 'cp .env.example .env'
                sh 'docker compose up -d'
                sh 'docker compose run --rm composer install'
                sh 'sleep 10 && docker compose run --rm artisan key:generate'
                sh 'docker compose run --rm artisan migrate'
                sh 'docker compose run --rm artisan test'
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

