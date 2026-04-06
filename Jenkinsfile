pipeline {
    agent any

    environment {
        VENV = "${WORKSPACE}/venv"
        PORT = "4000"
    }

    stages {

        stage('Setup & Clone') {
            steps {
                deleteDir()
                git branch: 'master', url: 'https://github.com/Avinashsain/flask_web_application.git'
            }
        }

        stage('Install') {
            steps {
                sh '''
                    python3 -m venv $VENV || true
                    $VENV/bin/pip install -r requirements.txt
                    $VENV/bin/pip install gunicorn
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    pkill -9 -f gunicorn || true
                    sleep 2

                    nohup $VENV/bin/gunicorn -w 2 -b 0.0.0.0:$PORT app:app > app.log 2>&1 &
                    sleep 5
                '''
            }
        }

        stage('Verify') {
            steps {
                sh '''
                    PUBLIC_IP=$(curl -s http://169.254.169.254/latest/meta-data/public-ipv4)

                    echo "Local check:"
                    curl -I http://localhost:$PORT || true

                    echo "Public check:"
                    curl -I http://$PUBLIC_IP:$PORT || true
                '''
            }
        }
    }

    post {
        success {
            echo "🚀 App Live on http://<public-ip>:${PORT}"
        }
        failure {
            echo "❌ Failed - check app.log"
        }
    }
}