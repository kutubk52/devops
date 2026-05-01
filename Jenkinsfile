pipeline {
    agent any

    parameters {
        file(name: 'CONFIG_JSON', description: 'Upload JSON config')
        string(name: 'BRANCH', defaultValue: 'main')
    }

    stages {

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
        stage('Setup SSH for Jenkins') {
            steps {
                sh """
                mkdir -p ~/.ssh
                chmod 700 ~/.ssh

                echo "Target host is: ${env.TARGET_HOST}"

                ssh-keyscan -H ${env.TARGET_HOST} >> ~/.ssh/known_hosts
                chmod 600 ~/.ssh/known_hosts

                cat ~/.ssh/known_hosts
                """
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
                ANSIBLE_HOST_KEY_CHECKING=False \
                ansible-playbook ansible/playbook.yml \
                -i '${env.TARGET_HOST},' \
                -u cloud_user \
                --extra-vars "script_name=${env.SCRIPT_NAME} message='${env.MESSAGE}' workspace=${env.WORKSPACE}"
                """
            }
        }
    }
}