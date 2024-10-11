pipeline {
    agent any

    environment {
        GIT_REPO = 'https://github.com/Meghatyagi03/AMI-Jenkins.git' // Updated repo URL
        BRANCH = 'main'
        PACKER_TEMPLATE = 'packer-template.json' // Provided Packer template
        AWS_REGION = 'us-east-1' // Provided AWS region
        AWS_CREDENTIALS_ID = 'Aws-cred' // Provided AWS credentials ID
        GIT_CREDENTIALS_ID = 'demo' // Provided Git credentials ID
    }

    stages {
        stage('Cleanup Workspace') {
            steps {
                echo 'Cleaning up workspace...'
                deleteDir()
            }
        }

        stage('Checkout GIT') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${GIT_CREDENTIALS_ID}", usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                    sh """
                    git clone https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/Meghatyagi03/AMI-Jenkins.git
                    cd AMI-Jenkins
                    git checkout ${BRANCH}
                    """
                }
            }
        }

        stage('Validate Packer Template') {
            steps {
                script {
                    dir('AMI-Jenkins') { // Ensure the command runs in the cloned repo directory
                        sh "packer validate ${PACKER_TEMPLATE}"
                    }
                }
            }
        }

        stage('Build AMI') {
            steps {
                script {
                    echo 'Start building AMI...'
                    dir('AMI-Jenkins') { // Ensure the command runs in the cloned repo directory
                        withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: "${AWS_CREDENTIALS_ID}"]]) {
                            sh '''
                            packer init .
                            packer build -var "region=${AWS_REGION}" ${PACKER_TEMPLATE}
                            '''
                        }
                    }
                }
            }
        }

        stage('AMI Build Status') {
            steps {
                echo 'AMI build successful'
            }
        }
    }

    post {
        success {
            echo 'Build completed successfully!'
        }
        failure {
            echo 'AMI build failed!'
        }
        always {
            echo 'Final cleanup...'
            deleteDir()
        }
    }
}
