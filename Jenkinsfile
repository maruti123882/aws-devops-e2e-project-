pipeline {
    agent any

    environment {
        IMAGE_NAME = "e2e-java-app"
        KUBECONFIG = "/var/lib/jenkins/.kube/config"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/maruti123882/aws-devops-e2e-project-.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Docker Build') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    docker build -t $DOCKER_USER/$IMAGE_NAME:latest .
                    '''
                }
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    docker push $DOCKER_USER/$IMAGE_NAME:latest
                    '''
                }
            }
        }

stage('Deploy to Kubernetes') {
    steps {
        sh '''
        export KUBECONFIG=/home/ubuntu/.kube/config

        echo "Using kubeconfig:"
        echo $KUBECONFIG

        kubectl config view
        kubectl get nodes

        kubectl apply -f deployment.yaml
        kubectl apply -f service.yaml
        '''
    }
}


