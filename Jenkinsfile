pipeline {
    agent any

    environment {
        VENV = "${WORKSPACE}/venv"
        PORT = "4000"
    }

    stages {

        stage('Clean & Clone') {
            steps {
                deleteDir()
                git branch: 'master', url: 'https://github.com/Avinashsain/flask_web_application.git'
            }
        }

        stage('Check Latest Code') {
            steps {
                sh 'git log -1'
            }
        }

        stage('Install') {
            steps {
                sh '''
                    python3 -m venv $VENV || true
                    $VENV/bin/pip install --upgrade pip
                    $VENV/bin/pip install -r requirements.txt
                    $VENV/bin/pip install gunicorn
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    echo "Stopping old app..."
                    pkill -9 -f gunicorn || true
                    pkill -9 -f app.py || true
                    sleep 2

                    echo "Starting app..."
                    cd $WORKSPACE

                    nohup $VENV/bin/gunicorn -w 2 -b 0.0.0.0:$PORT app:app > app.log 2>&1 &

                    sleep 5

                    echo "Check process:"
                    ps aux | grep gunicorn
                '''
            }
        }

        stage('Verify') {
            steps {
                sh '''
                    echo "Local test:"
                    curl -I http://localhost:$PORT || true

                    echo "Public IP fetch:"
                    PUBLIC_IP=$(curl -s http://169.254.169.254/latest/meta-data/public-ipv4 || echo "")

                    if [ -z "$PUBLIC_IP" ]; then
                        echo "Public IP not found ❌"
                    else
                        echo "Public IP: $PUBLIC_IP"
                        curl -I http://$PUBLIC_IP:$PORT || true
                    fi
                '''
            }
        }
    }

    post {
        success {
            echo "🚀 Deployment finished"
        }
        failure {
            echo "❌ Failed - check app.log"
        }
    }
}