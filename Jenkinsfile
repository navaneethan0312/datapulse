pipeline {
    agent any

    environment {
        SERVER_IP = '13.126.110.36'
        SERVER_USER = 'ubuntu'
        APP_DIR = '/home/ubuntu/datapulse'
    }

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
                sshagent(credentials: ['ssh-key']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ${SERVER_USER}@${SERVER_IP} "
                            mkdir -p ${APP_DIR}"

                        scp -o StrictHostKeyChecking=no -r * ${SERVER_USER}@${SERVER_IP}:${APP_DIR}

                        ssh -o StrictHostKeyChecking=no ${SERVER_USER}@${SERVER_IP} "
                            cd ${APP_DIR}

                            python3 -m venv venv || true
                            . venv/bin/activate
                            pip install -r requirements.txt

                             
                            python mange.py migrate || true
                            python mange.py collectstatic --noinput || true
                            

                            pkill -f gunicorn || true

                            nohup venv/bin/gunicorn datapulse.wsgi:application --bind 0.0.0.0:8000 > output.log 2>&1 &
                        "
                    """
                }
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
