pipeline {
    agent any

    tools {
        maven 'maven3'
        jdk 'java17'
    }

    environment {
        NEXUS_CREDENTIALS = credentials('nexus-creds')
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/sahinbinici/demo-App.git'
            }
        }
        stage('Build & Test') {
            steps {
                sh 'mvn clean verify -B'
            }
        }
        stage('Publish to Nexus') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'nexus-creds', usernameVariable: 'NEXUS_USER', passwordVariable: 'NEXUS_PASS')]) {
                    sh "mvn deploy -Dusername=$NEXUS_USER -Dpassword=$NEXUS_PASS -B"
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t demo-app:latest .'
            }
        }
        stage('Deploy Docker Container') {
            steps {
                sh 'docker rm -f demo-app || true'
                sh 'docker run -d -p 8085:8080 --name demo-app demo-app:latest'
            }
        }
    }

    post {
        success {
            echo "Pipeline başarıyla tamamlandı!"
        }
        failure {
            echo "Pipeline sırasında hata oluştu."
        }
    }
}
