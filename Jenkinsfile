pipeline {
    agent any

    parameters {
        file(name: 'CONFIG_JSON', description: 'Upload JSON config')
        string(name: 'BRANCH', defaultValue: 'main')
    }

    stages {
        cleanWs()

        stage('Checkout') {
            steps {
                git branch: "${params.BRANCH}",
                    url: 'https://github.com/kutubk52/devops.git'
            }
        }

        stage('Read JSON') {
            steps {
                script {
                    def json = readJSON file: params.CONFIG_JSON

                    if (!json.target_host) {
                        error("target_host missing!")
                    }

                    env.TARGET_HOST = json.target_host
                    env.SCRIPT_NAME = json.script_name
                    env.MESSAGE = json.message
                }
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