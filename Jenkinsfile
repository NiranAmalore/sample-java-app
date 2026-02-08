pipeline {
    agent any

    environment {
        SSH_CREDENTIALS = 'instance2-ssh'
        INSTANCE2_IP = '3.111.214.227'
        GITHUB_REPO = 'https://github.com/NiranAmalore/sample-java-app.git'
        DOCKER_IMAGE = 'niranamalore/sample-app:latest'
        DOCKERHUB_USERNAME = 'niranamalore'
        DOCKERHUB_PASSWORD = 'DoomSlayer2000'
    }

    stages {

        stage('Build on Instance 2') {
            steps {
                sshagent([env.SSH_CREDENTIALS]) {
                    sh """
                    ssh -o StrictHostKeyChecking=no ubuntu@${INSTANCE2_IP} '
                        sudo apt update -y
                        sudo apt install -y git maven docker.io openjdk-17-jdk

                        if [ -d sample-java-app ]; then
                            cd sample-java-app && git pull
                        else
                            git clone ${GITHUB_REPO}
                            cd sample-java-app
                        fi

                        mvn clean package
                    '
                    """
                }
            }
        }

        stage('Docker Build & Push') {
            steps {
                sshagent([env.SSH_CREDENTIALS]) {
                    sh """
                    ssh ubuntu@${INSTANCE2_IP} '
                        cd sample-java-app
                        sudo docker build -t ${DOCKER_IMAGE} .
                        echo ${DOCKERHUB_PASSWORD} | sudo docker login -u ${DOCKERHUB_USERNAME} --password-stdin
                        sudo docker push ${DOCKER_IMAGE}
                    '
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sshagent([env.SSH_CREDENTIALS]) {
                    sh """
                    ssh ubuntu@${INSTANCE2_IP} '
                        cd sample-java-app
                        kubectl apply -f k8s-deployment.yaml
                    '
                    """
                }
            }
        }
    }
}

