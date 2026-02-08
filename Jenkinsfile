pipeline {
    agent any

    stages {
        stage('Build on Instance 2') {
            steps {
                sshagent(['ubuntu']) {
                    sh '''
ssh -o StrictHostKeyChecking=no ubuntu@54.227.126.229 '
rm -rf sample-java-app &&
git clone https://github.com/NiranAmalore/sample-java-app.git &&
cd sample-java-app &&
mvn -q clean package &&
docker build -t sample-app . &&
kind load docker-image sample-app --name devops-cluster &&
kubectl apply -f k8s-deployment.yaml
'
'''
                }
            }
        }
    }
}
