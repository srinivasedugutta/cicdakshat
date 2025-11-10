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
                    sh "echo $PASS | docker login -u $USER --password-stdin"
                    sh 'docker push srinivasedugutta/oct302025project:v1'
                }
            }
        }

        stage('Deploy to k8s') {
            when { branch 'master' }
            steps {
                script {
                    kubernetesDeploy(
                        configs: 'deploymentservice.yaml',
                        kubeconfigId: 'k8sconfigpwd'
                    )
                }
            }
        }
    }
}
