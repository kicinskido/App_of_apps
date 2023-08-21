def backendDockerTag=""
def frontendDockerTag=""
def frontendImage="kicinskido/frontend"
def backendImage="kicinskido/backend"
def dockerRegistry=""
def registryCredentials="Dockerhub"


pipeline {
    agent {
        label 'agent'
    }
    
    tools {
        terraform 'Terraform'
    }

    stages {
        stage('Get Code') {
            steps {
                checkout scm
            }
        }

        stage('Clean running containers') {
            steps {
                sh "docker rm -f frontend backend"
            }
        }
        
        stage('Adjust version') {
            steps {
                script{
                    backendDockerTag = params.backendDockerTag.isEmpty() ? "latest" : params.backendDockerTag
                    frontendDockerTag = params.frontendDockerTag.isEmpty() ? "latest" : params.frontendDockerTag
                    
                    currentBuild.description = "Backend: ${backendDockerTag}, Frontend: ${frontendDockerTag}"
                }
            }
        }

        stage('Deploy application') {
            steps {
                script {
                    withEnv(["FRONTEND_IMAGE=$frontendImage:$frontendDockerTag", 
                             "BACKEND_IMAGE=$backendImage:$backendDockerTag"]) {
                       docker.withRegistry("$dockerRegistry", "$registryCredentials") {
                            sh "docker-compose up -d"
                        }
                    }
                }
            }
        }

        stage('Selenium tests') {
            steps {
                sh "pip3 install -r test/selenium/requirements.txt"
                sh "python3 -m pytest /home/panda/App_of_apps/test/selenium/frontend_test"
            }
        }
    }
}