// Define the variables
def dockerPublisherName = "rahulraju"
def dockerRepoName = "simple-php-app"

def gitRepoName = "https://github.com/lRAHULl/simple-php-app.git"
def customLocalImage = "sample-php-app-image"

def gitBranch

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
                git branch: "**", url: "${gitRepoName}"
                script {
                    gitBranch=getBranchName "${GIT_BRANCH}"
                }
                echo "CHECKING OUT BRANCH   ------  ${gitBranch}"
            }
        }


        stage('Build') {
            steps {
                script {
                    if (gitBranch == 'master' || gitBranch == 'develop'){
                        sh "docker rmi ${customLocalImage} || true"
                        sh "docker build -t ${customLocalImage} apache/"
                        sendSlackMessage "Build Successul"
                    } else if (gitBranch.contains('feature')) {
                        echo "It is a feature branch"
                    }
                }
            }
        }

        stage('Publish') {
            steps {
                script {
                    if (gitBranch == 'master' || gitBranch == 'develop'){
                        sh "docker tag ${customLocalImage} ${dockerPublisherName}/${dockerRepoName}:${gitBranch}-0.0.${BUILD_NUMBER}"
                        sh "docker tag ${customLocalImage} ${dockerPublisherName}/${dockerRepoName}:latest"
                        sh "docker push ${dockerPublisherName}/${dockerRepoName}"
                        sendSlackMessage "Publish Successul"
                    } else if (gitBranch.contains('feature')) {
                        echo "It is a feature branch"
                    }
                }
            }
        }
        
        stage('Stage') {
            steps {
                script {
                    if (gitBranch == 'master' || gitBranch == 'develop'){
                        sh "docker stop lamp-web || true"
                        sh "docker rm lamp-web || true"
                        sh "docker stop lamp-mysql || true"
                        sh "docker rm lamp-mysql || true"
                        
                        sh "docker-compose up -d"
                    } else if (gitBranch.contains('feature')) {
                        echo "It is a feature branch"
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                publishInECS()
                script {
                    if (gitBranch == 'master'){
                        echo "Master "
                        // sh "docker stop lamp-web || true"
                        // sh "docker rm lamp-web || true"
                        // sh "docker stop lamp-mysql || true"
                        // sh "docker rm lamp-mysql || true"
                        
                        // sh "docker-compose up -d"
                    } else if (gitBranch.contains('feature')) {
                        echo "It is a feature branch"
                    }
                }
            }
        }

        stage('Clean') {
            steps {
                script {
                    if (gitBranch == 'master' || gitBranch == 'develop'){
                        sh "docker rmi ${customLocalImage} || true"
                        sh "docker system prune -f || true"
                    } else if (gitBranch.contains('feature')) {
                        echo "It is a feature branch"
                    }
                }
            }
        }

    }
}

void publishInECS() {
    sh """
        aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin ${ECS_REGISTRY}
        docker tag ${customLocalImage} ${ECS_REGISTRY}/jenkins-test-repo:${BUILD_NUMBER} apache/
        docker tag ${customLocalImage} ${ECS_REGISTRY}/jenkins-test-repo:latest apache/
        echo "${ECS_REGISTRY}/jenkins-test-repo"
        docker push ${ECS_REGISTRY}/jenkins-test-repo
    """
}

String getBranchName(String inputString) {
    return inputString.split("/")[1]
}

void sendSlackMessage(String message) {
    slackSend botUser: true, channel: 'private_s3_file_upload', failOnError: true, message: "Message From http://3.231.228.54:8080/: \n${message}", teamDomain: 'codaacademy2020', tokenCredentialId: 'coda-academy-slack'
}