CODE_CHANGES = getGitChanges()

def gv 

pipeline {

    agent any

    tools {
        maven 'Maven'
    }

    parameters {
        string(name: 'VERSION', defaultValue: '', description: 'version to deploy')
        choice(name: 'VERSION', choices: ['1.1.0', '1.2.0', '1.3.0'], description: '')
        booleanParam(name: 'executeTests', defaultValue: true, description: '')
    }

    environment {
        NEW_VERSION = '1.3.0'
        SERVER_CREDENTIALS = credentials('credential-id')
    }

    stages {

        stage("init") {

            steps {

                script {
                    gv = load "script.groovy"
                }

            }

        }

        stage("build") {

            when {
                expression {
                    BRANCH_NAME == 'dev' && CODE_CHANGES == true
                }
            }

            steps {

                echo 'building the application'
                echo "building the version ${NEW_VERSION}"

                script {
                    gv.buildApp()
                }

            }
        }

        stage("test") {

            when {
                expression {
                    BRANCH_NAME == 'dev' || BRANCH_NAME == 'master'
                    params.executeTests == true
                }
            }

            steps {

                echo 'testing the application'

                script {
                    gv.testApp()
                }

            }
        }

        stage("deploy") {

            steps {

                echo 'deploying the application'
                echo "deploying with ${SERVER_CREDENTIALS}"
                sh "${SERVER_CREDENTIALS}"
                withCredentials([
                    usernamePassword(credentials: 'credential-id', usernameVariable: USER, passwordVariable: PWD)
                ]) {

                    sh "some script ${USER} ${PWD}"

                }

                echo "deploying version ${VERSION}"

                script {
                    gv.deployApp()
                }


            }
        }
    }

    post {

        always {

        }

        success {

        }

        failure {

        }

    }
}
