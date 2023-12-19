// pipeline {
//     agent any

//     environment {
//         SSH_CREDENTIALS_ID = 'site' // Replace with your actual SSH credentials ID
//         SERVER_IP = 'ec2-54-152-195-130.compute-1.amazonaws.com'
//         REMOTE_USER = 'ubuntu' // Change this to the appropriate non-root user
//         GITHUB_REPO_URL = 'https://github.com/Mutuwa99/thato_pro.git'
//     }

//     stages {
//         stage('Prepare') {
//             steps {
//                 script {
//                     // Clean workspace
//                     deleteDir()

//                     // Checkout the code from the GitHub repository
//                     checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: GITHUB_REPO_URL]]])
//                 }
//             }
//         }

//         stage('Test SSH Connection') {
//             steps {
//                 script {
//                     // Use SSH credentials to test the connection
//                     withCredentials([file(credentialsId: SSH_CREDENTIALS_ID, variable: 'SSH_KEY')]) {
//                         // Add the server's host key to known_hosts
//                         sh "ssh-keyscan -H ${SERVER_IP} >> ~/.ssh/known_hosts"

//                         // Test the connection
//                         sh "ssh -i \$SSH_KEY -o StrictHostKeyChecking=no ${REMOTE_USER}@${SERVER_IP} 'echo Connection successful'"
//                     }
//                 }
//             }
//         }

//         stage('Deploy Static Website') {
//             steps {
//                 script {
//                     // Ensure remote directory exists
//                    // sh "ssh -i \$SSH_KEY ${REMOTE_USER}@${SERVER_IP} 'mkdir -p /var/www/html/'"

//                     // Deploy the code tohhhh the server using scp
//                     withCredentials([file(credentialsId: SSH_CREDENTIALS_ID, variable: 'SSH_KEY')]) {
//                         try {
//                             sh "scp -i \$SSH_KEY -o StrictHostKeyChecking=no -r ./ ${REMOTE_USER}@${SERVER_IP}:/var/www/html/"
//                             echo 'Deployment successful!'
//                         } catch (Exception e) {
//                             echo "Deployment failed: ${e.message}"
//                             error 'Deployment failed!'
//                         }
//                     }
//                 }
//             }
//         }
//     }

//     post {
//         success {
//             echo 'Pipeline successful!'
//         }
//         failure {
//             echo 'Pipeline failed!'
//         }
//     }
// }

pipeline {
    agent any

    environment {
        SONAR_SCANNER_HOME = tool 'sonarqube'
        // Other environment variables...
    }

    stages {
        stage('SonarQube Analysis') {
            steps {
                script {
                    // Run the SonarQube Scanner analysis
                    sh "${SONAR_SCANNER_HOME}/bin/sonar-scanner -Dsonar.projectKey=isaya -Dsonar.sources=. -Dsonar.host.url=http://54.227.109.162:9000 -Dsonar.login=sqp_73910cf48fb5b28718c4932858642fc01a090b56"
                }
            }
        }
        // Other stages...
    }

    post {
        success {
            echo 'SonarQube Analysis successful!'
        }
        failure {
            echo 'SonarQube Analysis failed!'
        }
    }
}
