pipeline {
    agent any
    environment {
        APP_DIR = "${WORKSPACE}"
        VENV_DIR = "${WORKSPACE}/venv"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/Avinashsain/flask_web_application.git'
            }
        }

        stage('Setup Python Environment') {
            steps {
                sh '''
                python3 -m venv $VENV_DIR || true
                $VENV_DIR/bin/pip install --upgrade pip
                $VENV_DIR/bin/pip install -r requirements.txt
                '''
            }
        }

        stage('Deploy Flask App') {
            steps {
                sh '''
                # Kill old process
                pkill -f app.py || true
                sleep 2

                # Start Flask using Gunicorn so it survives Jenkins
                $VENV_DIR/bin/pip install gunicorn || true
                nohup $VENV_DIR/bin/gunicorn -w 4 -b 0.0.0.0:5000 app:app > app.log 2>&1 &
                '''
            }
        }
    }

    post {
        success {
            echo "Deployment Successful 🚀"
        }
        failure {
            echo "Deployment Failed ❌ Check app.log for details"
        }
    }
}