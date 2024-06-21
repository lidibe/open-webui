pipeline {
    agent any
    environment {
        projectname = "ollama-webui"
        version = "master"
        BRANCH = "master"
        SLACK_CHANNEL = 'afxm-pipelines'
        SLACK_DOMAIN  = 'afb-it'
        SLACK_CREDENTIALS = 'Slack1'
        CHANGE_LIST = true
        TEST_SUMMARY = true
        registry = "378457291432.dkr.ecr.eu-west-1.amazonaws.com"
        REGION = "eu-west-1"
        EB_BUCKET = "elasticbeanstalk-eu-west-1-378457291432"
        ENC = ""
        VERSION = "experimental"
        TIMESTAMP=java.time.LocalDateTime.now()
    }
    stages {
        stage("Checkout WebUI") {
            steps {
                    slackSend (color: '#FFFF00', message: "STARTED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
                    git branch: 'experimental',
                        credentialsId: 'ollama-webui',
                        url: 'git@github.com:lidibe/open-webui'
               }
        }
        stage("WebUI (Build)") {
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    dir("${env.WORKSPACE}"){
                        sh "pwd"
                        echo '>>> ---------- Start Building the WebUI Image -------------'
                        sh "docker build -t afxm-ollama-webui:${env.VERSION} . "
                        echo '<<< ---------- End Building the WebUI Image -------------'
                    }
                }
            }
            post {
                failure {
                    slackSend (channel: "${env.SLACK_CHANNEL}",  color: '#d51400', message: "<<< ---------- BUILD | WebUI Image failed -------------")
                }
            }
        }
        stage("Docker Push") {
            steps {
            	sh "aws ecr get-login-password | docker login --username AWS --password-stdin https://378457291432.dkr.ecr.eu-west-1.amazonaws.com"
                sh "docker tag afxm-erm-service:latest 378457291432.dkr.ecr.eu-west-1.amazonaws.com/afxm-ollama-webui:${env.VERSION}"
                sh "docker push 378457291432.dkr.ecr.eu-west-1.amazonaws.com/afxm-ollama-webui:${env.VERSION}"
            }
        }
    }
}
