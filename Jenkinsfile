pipeline {
    agent any

    environment {
        SSH_CREDENTIALS_ID = 'mysite'
        SERVER_IP = 'ec2-54-152-195-130.compute-1.amazonaws.com'
        REMOTE_USER = 'ubuntu'
        GITHUB_REPO_URL = 'https://github.com/Mutuwa99/thato_pro.git'
    }

    stages {
        stage('Prepare') {
            steps {
                script {
                    // Clean workspace
                    deleteDir()

                    // Checkout the code only if changes are detected
                    checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: GITHUB_REPO_URL]]])
                }
            }
        }

        stage('Test SSH Connection') {
            steps {
                script {
                    // Use SSH credentials to test the connection
                    withCredentials([file(credentialsId: SSH_CREDENTIALS_ID, variable: 'SSH_KEY')]) {
                        sh "ssh-keyscan -H ${SERVER_IP} >> ~/.ssh/known_hosts"
                        // sh "ssh -i \$SSH_KEY -o StrictHostKeyChecking=no ${REMOTE_USER}@${SERVER_IP} 'echo Connection successful'"
                        sh "ssh -i ${SSH_KEY} -o StrictHostKeyChecking=no ${REMOTE_USER}@${SERVER_IP} 'echo Connection successful'"

                    }
                }
            }
        }

        stage('Deploy Static Website') {
            steps {
                script {
                    // Deploy only if changes are detected
                    if (isChangesDetected()) {
                        deployWebsite()
                        echo 'Deployment successful!'
                    } else {
                        echo 'No changes detected. Skipping deployment.'
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline successful!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}

def isChangesDetected() {
    // Check if changes are detected
    return script(returnStatus: true, script: 'git diff --exit-code') == 1
}

def deployWebsite() {
    // Deploy the code to the server using scp
    withCredentials([file(credentialsId: SSH_CREDENTIALS_ID, variable: 'SSH_KEY')]) {
        try {
            sh "scp -i \$SSH_KEY -o StrictHostKeyChecking=no -r ./ ${REMOTE_USER}@${SERVER_IP}:/var/www/html/"
        } catch (Exception e) {
            echo "Deployment failed: ${e.message}"
            error 'Deployment failed!'
        }
    }
}
