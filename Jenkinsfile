pipeline {
    agent any

    environment {
        NODE_ENV = 'production'
        EC2_USER = 'ubuntu'
        EC2_IP = '98.81.245.108'
        REMOTE_PATH = '/home/ubuntu/node_deploy'
        SSH_KEY = credentials('ssh-key-ec2')
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/roberto14118927/node_deploy.git'
            }
        }

        stage('Build') {
            steps {
                 sh 'rm -rf node_modules'
                sh 'npm ci'
                    sh 'npm run build'
            }
        }

        stage('Deploy') {
            steps {
                sh """
                ssh -i $SSH_KEY -o StrictHostKeyChecking=no $EC2_USER@$EC2_IP '
                    cd $REMOTE_PATH &&
                    git pull origin main &&
                    npm install &&
                    npm run build &&
                    pm2 restart my-api || pm2 start dist/server.js --name my-api
                '
                """
            }
        }
    }
}
