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

                script {
                    if (gitBranch == 'master'){
                        echo "Master "

                        ECS_REGISTRY="572508813856.dkr.ecr.us-east-1.amazonaws.com"
                        ECR_REPO="jenkins-test-repo"
                        
                        // sh 'bash ./aws-ecs-deploy.sh'

                        sh """
                            docker tag ${customLocalImage} ${ECS_REGISTRY}/${ECR_REPO}:0.0.${BUILD_NUMBER}
                            docker tag ${customLocalImage} ${ECS_REGISTRY}/${ECR_REPO}:latest
                            echo "${ECS_REGISTRY}/${ECR_REPO}"
                            docker push ${ECS_REGISTRY}/${ECR_REPO}
                        """
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

// void publishInECS(String customLocalImage) {
//     // echo "${customLocalImage}"
    
// }

void deployToECS() {
    sh '''

        dockerRepo=`aws ecr describe-repositories --repository-name jenkins-test-repo --region us-east-1 | grep repositoryUri | cut -d "\"" -f 4`
        sed -e "s;DOCKER_IMAGE_NAME;${dockerRepo}:latest;g" ${WORKSPACE}/template.json > taskDefinition.json
        aws ecs register-task-definition --family jenkins-test --cli-input-json file://taskDefinition.json --region us-east-1
        revision=`aws ecs describe-task-definition --task-definition jenkins-test --region us-east-1 | grep "revision" | tr -s " " | cut -d " " -f 3`
        aws ecs update-service --cluster test-cluster --service test-service --task-definition jenkins-test:${revision} --desired-count 1

    '''
}

String getBranchName(String inputString) {
    return inputString.split("/")[1]
}

void sendSlackMessage(String message) {
    slackSend botUser: true, channel: 'private_s3_file_upload', failOnError: true, message: "Message From http://3.231.228.54:8080/: \n${message}", teamDomain: 'codaacademy2020', tokenCredentialId: 'coda-academy-slack'
}