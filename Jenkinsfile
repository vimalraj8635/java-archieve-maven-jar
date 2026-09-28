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
                sh 'mvn clean package -DskipTests -Dcheckstyle.skip=true'
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

        stage('Deploy') {
            steps {
                withCredentials([
                    sshUserPrivateKey(
                        credentialsId: 'app-ec2-key',
                        keyFileVariable: 'SSH_KEY',
                        usernameVariable: 'SSH_USER'
                    )
                ]) {
                    sh '''
                        echo "Copying application JAR..."

                        scp -i "$SSH_KEY" \
                            -o StrictHostKeyChecking=no \
                            target/*.jar \
                            "$SSH_USER@172.31.4.32:/opt/java-app/app.jar"

                        echo "Restarting application..."

                        ssh -i "$SSH_KEY" \
                            -o StrictHostKeyChecking=no \
                            "$SSH_USER@172.31.4.32" \
                            "sudo systemctl restart java-app"

                        echo "Deployment completed successfully!"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '🎉 CI/CD Pipeline completed successfully!'
        }

        failure {
            echo '❌ CI/CD Pipeline failed. Check the console output.'
        }
    }
}
