pipeline {
    agent any

    environment {
        gitRepo = 'https://github.com/Meghatyagi03/AMI-Jenkins.git' 
        branch = 'main'
        packerTemplate = 'packer-template.json'
        awsRegion = 'us-east-1'
        awsCredentialsId = 'Aws-cred'  // Use the correct AWS credentials ID here
        gitCredentialsId = 'demo'
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

        stage('Initialize Packer') {
            steps {
                script {
                    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: "${awsCredentialsId}"]]) {
                        writeFile file: 'packer-config.pkr.hcl', text: '''
packer {
  required_plugins {
    amazon = {
      source  = "github.com/hashicorp/amazon"
      version = "~> 1"
    }
  }
}
'''
                        sh "packer init ."
                    }
                }
            }
        }

        stage('Validate Packer Template') {
            steps {
                script {
                    sh "packer validate ${packerTemplate}"
                }
            }
        }

        stage('Build AMI') {
            steps {
                script {
                    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: "${awsCredentialsId}"]]) {
                        sh """
                        packer build -var 'aws_region=${awsRegion}' ${packerTemplate}
                        """
                    }
                }
            }
        }
    }
}
