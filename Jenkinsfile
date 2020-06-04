// Define the variables
def dockerPublisherName = "rahulraju"
def dockerRepoName = "simple-php-app"

def gitRepoName = "https://github.com/lRAHULl/simple-php-app.git"
def gitBranch = "master"

def customLocalImage = "sample-php-app-image"

properties([pipelineTriggers([githubPush()])])
pipeline {
    agent {
        node {
            label 'linux-slave'
        }
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: "${gitBranch}", url: "${gitRepoName}"
            }
        }


        stage('Build') {
            steps {
                // sh './gradlew bootjar'
                sh "docker rmi ${customLocalImage} || true"
                sh "docker build -t ${customLocalImage} apache/"
                sendSlackMessage "Build Successul"
            }
        }

        stage('Publish') {
            steps {
                sh "docker tag ${customLocalImage} ${dockerPublisherName}/${dockerRepoName}:alpha-0.0.${BUILD_NUMBER}"
                sh "docker tag ${customLocalImage} ${dockerPublisherName}/${dockerRepoName}:latest"
                sh "docker push ${dockerPublisherName}/${dockerRepoName}"
                sendSlackMessage "Publish Successul"
            }
        }
        
        stage('Deploy') {
            steps {
                sh "docker stop lamp-web || true"
                sh "docker rm lamp-web || true"
                sh "docker stop lamp-mysql || true"
                sh "docker rm lamp-mysql || true"
                
                sh "docker-compose up -d"
            }
        }

        stage('Clean') {
            steps {
                sh "docker rmi ${customLocalImage} || true"
                sh "docker system prune -f || true"
            }
        }

    }
}
void sendSlackMessage(String message) {
    slackSend botUser: true, channel: 'private_s3_file_upload', failOnError: true, message: "Message From http://3.231.228.54:8080/: \n${message}", teamDomain: 'codaacademy2020', tokenCredentialId: 'coda-academy-slack'
}