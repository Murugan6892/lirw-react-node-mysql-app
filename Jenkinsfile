pipeline {
    agent {
        label 'prod-server'
    }

    tools {
        nodejs 'nodejs'
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

        stage('Deploy to Prod Server') {
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
            echo 'Pipeline executed successfully.'
        }

        failure {
            echo 'Pipeline failed.'
        }
    }
}
