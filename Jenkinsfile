pipeline {
    agent any
    tools {
        maven 'maven'
    }
    environment {
        register = 'public.ecr.aws/n4e0r7f3/onlinebook'
       
    }
    stages {
        stage('checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/srikanthbathini/bookstore.git']]])
            }
        }
        stage ('Build'){
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Build Docker Image'){
            steps {
                script {
                    echo "print build id ${env.BUILD_ID}"
                    
                   def customimage = docker.build("public.ecr.aws/n4e0r7f3/onlinebook:${env.BUILD_ID}")
                   sh 'aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/n4e0r7f3'
                   customimage.push()
                }
            }
        }
        stage('k8 deploy'){
            steps{
                script {
                    sh "sed -i 's/BUILD_ID/${env.BUILD_ID}/g' bookstore.yaml"
                    kubeconfig(credentialsId: 'kubernetes', serverUrl: '') {
                        sh 'kubectl create -f bookstore.yaml'
    
}
                }
            }
        }
    }
}

