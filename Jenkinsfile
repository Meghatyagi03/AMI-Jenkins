pipeline {
    agent any

    tools {
        git 'Default'  // Make sure the label matches the Git installation name
    }

    environment {
        gitRepo = 'https://github.com/Meghatyagi03/AMI-Jenkins.git' 
        branch = 'main'
        packerTemplate = 'packer-template.json'
        awsRegion = 'us-east-1'
        awsCredentialsId = 'Aws-cred'
        gitCredentialsId = 'AMI' 
    }

    stages {
        stage('Checkout GIT') {
            steps {
                script {
                    withCredentials([string(credentialsId: "${gitCredentialsId}", variable: 'GIT_TOKEN')]) {
                        git url: "https://${GIT_TOKEN}@github.com/Meghatyagi03/AMI-Jenkins.git", branch: "${branch}"
                    }
                }
            }
        }

        // Other stages remain unchanged...
    }
}
