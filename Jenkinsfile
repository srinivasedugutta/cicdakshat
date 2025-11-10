pipeline {
    agent any
    stages {
        stage('Build Maven') {
            steps {
                git url: 'https://github.com/srinivasedugutta/cicdakshat/', branch: 'master'
                sh 'mvn clean install'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t srinivasedugutta/oct302025project:v1 .'
                }
            }
        }

        stage('Docker Login & Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-pwd', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    sh """
                        echo $PASS | docker login -u $USER --password-stdin
                        docker push srinivasedugutta/oct302025project:v1
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            when { branch 'master' }
            steps {
                withCredentials([string(credentialsId: 'kubeconfig-text', variable: 'KUBECONFIG_CONTENT')]) {
                    script {
                        sh '''
                        # Set up kubeconfig
                        mkdir -p ~/.kube
                        echo "$KUBECONFIG_CONTENT" > ~/.kube/config
                        chmod 600 ~/.kube/config

                        # Deploy to cluster
                        echo "Deploying application to Kubernetes..."
                        kubectl apply -f deploymentservice.yaml

                        # Check status
                        kubectl get pods
                        kubectl get svc
                        '''
                    }
                }
            }
        }
    }
}
