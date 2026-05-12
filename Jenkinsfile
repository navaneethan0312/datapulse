pipeline {
    agent any

    environment {
        SERVER_IP = '13.126.110.36'
        SERVER_USER = 'ubuntu'
        APP_DIR = '/home/ubuntu/datapulse'
    }=

    stages {

        stage('Clone Repository') {
            steps {
                git branch: 'main',
                url: 'https://github.com/navaneethan0312/datapulse.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                '''
            }
        }

    stage('Deploy to Remote Server') {
    steps {
        sshagent(credentials: ['shh-key']) {
            sh """
                ssh -o StrictHostKeyChecking=no ${SERVER_USER}@${SERVER_IP} '
                    if [ ! -d "${APP_DIR}" ]; then
                        git clone https://github.com/navaneethan0312/datapulse.git ${APP_DIR}
                    else
                        cd ${APP_DIR}
                        git pull origin main
                    fi

                    cd ${APP_DIR}

                    docker compose down || true
                    docker compose up -d --build
                '
            """
        }
    }
}

    post {
        success {
            echo 'Deployment completed successfully'
        }

        failure {
            echo 'Deployment failed'
        }
    }
}
