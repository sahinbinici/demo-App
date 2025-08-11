pipeline {
    agent any
    tools {
        maven 'Maven3'  // Jenkins Global Tool Configuration'da tanımladığın Maven ismi
        jdk 'Java17'    // Jenkins Global Tool Configuration'da tanımladığın JDK ismi
    }
    environment {
        NEXUS_CREDENTIALS = credentials('nexus-creds')  // Jenkins'de eklediğin credentials ID'si
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/sahinkinici/demo-app.git'

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

