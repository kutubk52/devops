pipeline {
    agent any

    parameters {
        file(name: 'CONFIG_JSON', description: 'Upload JSON config')
        string(name: 'BRANCH', defaultValue: 'main')
    }

    stages {

        stage('Debug SSH') {
            steps {
                sh '''
                whoami
                echo "HOME=$HOME"
                ls -l ~/.ssh
                '''
            }
        }
        stage('Read JSON') {
            steps {
                script {

                    echo "JSON TEXT: ${params.CONFIG_JSON_TEXT}"

                    if (!params.CONFIG_JSON_TEXT?.trim()) {
                        error("❌ CONFIG_JSON_TEXT is empty!")
                    }

                    def json = readJSON text: params.CONFIG_JSON_TEXT

                    env.TARGET_HOST = json.target_host
                    env.SCRIPT_NAME = json.script_name
                    env.MESSAGE = json.message

                    echo "Target: ${env.TARGET_HOST}"
                }
            }
        }

        stage('Checkout') {
            steps {
                cleanWs()   // now safe

                git branch: "${params.BRANCH}",
                    url: 'https://github.com/kutubk52/devops.git'

                sh "git log -1"
            }
        }

        stage('Run Ansible') {
            steps {
                sh """
                ansible-playbook ansible/playbook.yml \
                -i '${TARGET_HOST},' \
                --extra-vars "script_name=${SCRIPT_NAME} message='${MESSAGE}' workspace=${WORKSPACE}"
                """
            }
        }
    }
}