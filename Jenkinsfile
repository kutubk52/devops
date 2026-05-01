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

                    echo "CONFIG_JSON param = ${params.CONFIG_JSON}"

                    if (!params.CONFIG_JSON) {
                        error("❌ CONFIG_JSON not uploaded or not passed!")
                    }

                    sh "ls -l"

                    def json = readJSON file: params.CONFIG_JSON

                    env.SCRIPT_NAME = json.script_name
                    env.MESSAGE = json.message
                    env.TARGET_HOST = json.target_host
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