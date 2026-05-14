pipeline {

    agent {
        label 'prod-server'
    }

    environment {
        APP_NAME = 'lirw-react-node-mysql-app'
    }

    stages {

        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                credentialsId: 'github-pat',
                url: 'https://github.com/Murugan6892/lirw-react-node-mysql-app.git'
            }
        }

        stage('Check Node and NPM Version') {
            steps {
                sh 'node -v'
                sh 'npm -v'
            }
        }

        stage('Install Backend Dependencies') {
            steps {
                dir('backend') {
                    sh 'npm install'
                }
            }
        }

        stage('Install Frontend Dependencies') {
            steps {
                dir('frontend') {
                    sh 'npm install'
                }
            }
        }

        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    sh 'npm run build'
                }
            }
        }

        stage('Deploy Backend Application') {
            steps {
                dir('backend') {
                    sh '''
                    pkill node || true
                    nohup node server.js > app.log 2>&1 &
                    '''
                }
            }
        }

    }

    post {

        success {
            echo 'Application deployed successfully on prod-server.'
        }

        failure {
            echo 'Pipeline failed.'
        }

    }
}
