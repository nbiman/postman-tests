pipeline {
    agent any

    environment {
        GIT_CREDENTIALS = credentials('GitHub_SSH_Key') // ID of the SSH key credential from Jenkins
    }

    stages {
        stage('Clone Repo') {
            steps {
                script {
                    // Using the ssh-agent to authenticate using the provided credentials
                    sshagent([env.GIT_CREDENTIALS]) {
                        // Clone the repository from GitHub using the SSH URL
                        sh 'git clone -b main git@github.com:nbiman/postman-tests.git'
                    }
                }
            }
        }

        stage('Run Newman Tests') {
            steps {
                script {
                    // Make sure the Postman collection is in the correct location
                    sh '''
                    mkdir -p newman
                    cp "Regression Testing.postman_collection.json" "Regression_Testing.json"
                    /opt/homebrew/bin/newman run "Regression_Testing.json" --reporters cli,junit --reporter-junit-export "newman/newman-report.xml"
                    '''
                }
            }
        }

        stage('Publish Test Results') {
            steps {
                junit '**/newman/newman-report.xml'  // Specify the location of the Newman test report
            }
        }
    }
}
