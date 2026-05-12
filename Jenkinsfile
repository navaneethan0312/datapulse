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

        stage('Deploy to Remote Server') {
            steps {
                sshagent(credentials: ['ssh-key']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ${SERVER_USER}@${SERVER_IP} '

                            # Install Docker if not installed
                            if ! command -v docker >/dev/null 2>&1; then
                                sudo apt update -y
                                sudo apt install docker.io docker-compose-v2 git -y
                                sudo systemctl start docker
                                sudo systemctl enable docker
                                sudo usermod -aG docker ubuntu
                            fi

                            # Remove invalid folder if not a git repo
                            if [ -d "${APP_DIR}" ] && [ ! -d "${APP_DIR}/.git" ]; then
                                rm -rf ${APP_DIR}
                            fi

                            # Clone or pull latest code
                            if [ ! -d "${APP_DIR}" ]; then
                                git clone https://github.com/navaneethan0312/datapulse.git ${APP_DIR}
                            else
                                cd ${APP_DIR}
                                git pull origin main
                            fi

                            cd ${APP_DIR}
                            chmod +x entrypoint.sh

                            sudo docker compose down || true
                            sudo docker compose up -d --build
                        '
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful'
        }

        failure {
            echo 'Deployment Failed'
        }
    }
}
