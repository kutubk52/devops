pipeline {
    agent any

    parameters {
        file(name: 'CONFIG_JSON', description: 'Upload JSON config')
        string(name: 'BRANCH', defaultValue: 'main')
    }

    stages {
        stage('Checkout') {
            steps {
                cleanWs()

                checkout([
                    $class: 'GitSCM',
                    branches: [[name: "*/${params.BRANCH}"]],
                    userRemoteConfigs: [[
                        url: 'https://github.com/kutubk52/devops.git'
                    ]],
                    extensions: [
                        [$class: 'CleanBeforeCheckout']
                    ]
                ])

            sh "git log -1"
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