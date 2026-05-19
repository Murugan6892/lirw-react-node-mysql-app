pipeline {

    agent none

    environment {
        GIT_REPO = 'https://github.com/Murugan6892/lirw-react-node-mysql-app.git'
        BRANCH = 'main'

        DEPLOY_USER = 'ubuntu'
        DEPLOY_HOST = '100.48.16.230'
        DEPLOY_SSH = 'ec2-deploy-key'

        REMOTE_BASE = '/home/ubuntu/lirw-app'

        FRONTEND_DIR = "${REMOTE_BASE}/frontend"
        BACKEND_DIR = "${REMOTE_BASE}/backend"

        FRONTEND_BUILD = 'dist'
    }

    stages {

        stage('Checkout Code') {
            agent { label 'test-server' }

            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: "*/${BRANCH}"]],
                    userRemoteConfigs: [[
                        url: "${GIT_REPO}",
                        credentialsId: 'githun-test-pat'
                    ]]
                ])
            }
        }

        stage('Build Frontend') {
            agent { label 'test-server' }

            steps {

                dir('frontend') {

                    sh '''
                        set -e

                        echo "Node Version:"
                        node -v

                        echo "NPM Version:"
                        npm -v

                        echo "Installing frontend packages..."
                        npm install

                        echo "Building frontend..."
                        npm run build
                    '''
                }
            }
        }

        stage('Install Backend Dependencies') {
            agent { label 'test-server' }

            steps {

                dir('backend') {

                    sh '''
                        set -e

                        echo "Installing backend packages..."
                        npm install
                    '''
                }
            }
        }

        stage('Deploy Application') {
            agent { label 'test-server' }

            steps {

                sshagent(credentials: ["${DEPLOY_SSH}"]) {

                    sh """
                        set -e

                        echo "Creating remote directory..."

                        ssh -o StrictHostKeyChecking=no ${DEPLOY_USER}@${DEPLOY_HOST} '
                            mkdir -p ${REMOTE_BASE}
                        '

                        echo "Syncing project files..."

                        rsync -az \
                            --exclude='node_modules' \
                            --exclude='.git' \
                            ./ ${DEPLOY_USER}@${DEPLOY_HOST}:${REMOTE_BASE}/

                        echo "Starting backend application..."

                        ssh -o StrictHostKeyChecking=no ${DEPLOY_USER}@${DEPLOY_HOST} <<ENDSSH

                            set -e

                            cd ${BACKEND_DIR}

                            npm install

                            pkill node || true

                            nohup node server.js > app.log 2>&1 &

                            echo "Application started successfully"

ENDSSH
                    """
                }
            }
        }

    }

    post {

        success {
            echo 'Deployment completed successfully.'
        }

        failure {
            echo 'Pipeline failed.'
        }
    }
}
