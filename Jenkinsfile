pipeline {
    agent any

    environment {
        NODE_ENV = 'production'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/ahmadSalman267/todos-app.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'yarn install'
            }
        }

        stage('Build') {
            steps {
                sh 'yarn build || echo "No build step defined"'
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent (credentials: ['jenkins-ec2-key']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@54.161.60.66 <<EOF
                        if [ -d "todos-app" ]; then
                            cd todos-app
                            git pull origin main
                        else
                            git clone https://github.com/ahmadSalman267/todos-app.git
                            cd todos-app
                        fi
                        yarn install
                        pm2 restart all || pm2 start npm --name "todos-app" -- start
                    EOF
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ Deployment successful!'
        }
        failure {
            echo '❌ Deployment failed.'
        }
    }
}
