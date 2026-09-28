pipeline {

    agent any

    tools {
        maven 'Maven-3.9'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/vimalraj8635/java-archieve-maven-jar.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Test SSH Connection') {
    steps {
        withCredentials([
            sshUserPrivateKey(
                credentialsId: 'app-ec2-key',
                keyFileVariable: 'SSH_KEY',
                usernameVariable: 'SSH_USER'
            )
        ]) {
            sh '''
                ssh -i "$SSH_KEY" \
                    -o StrictHostKeyChecking=no \
                    "$SSH_USER@172.31.4.32" \
                    "hostname && whoami"
            '''
        }
    }
}

    }
}
