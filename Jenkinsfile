pipeline {
    agent any

    parameters {
        file(name: 'CONFIG_JSON', description: 'Upload JSON config')
        string(name: 'BRANCH', defaultValue: 'main')
    }

    stages {

        stage('Read JSON FIRST') {
            steps {
                script {

                    echo "CONFIG_JSON param = ${params.CONFIG_JSON}"

                    if (!params.CONFIG_JSON) {
                        error("❌ CONFIG_JSON not uploaded!")
                    }

                    def json = readJSON file: params.CONFIG_JSON

                    env.SCRIPT_NAME = json.script_name
                    env.MESSAGE = json.message
                    env.TARGET_HOST = json.target_host
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