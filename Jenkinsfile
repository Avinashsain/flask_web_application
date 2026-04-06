pipeline {
    agent any

    environment {
        VENV_DIR = "${WORKSPACE}/venv"
        FLASK_PORT = "4000"
    }

    stages {

        stage('Clean Workspace') {
            steps {
                deleteDir()
            }
        }

        stage('Clone Latest Code') {
            steps {
                git branch: 'master', url: 'https://github.com/Avinashsain/flask_web_application.git'
            }
        }

        stage('Verify Latest Code') {
            steps {
                sh 'git log -1'
            }
        }

        stage('Setup Environment') {
            steps {
                sh '''
                    python3 -m venv $VENV_DIR
                    $VENV_DIR/bin/pip install --upgrade pip
                    $VENV_DIR/bin/pip install -r requirements.txt
                    $VENV_DIR/bin/pip install gunicorn
                '''
            }
        }

        stage('Deploy App') {
            steps {
                sh '''
                    echo "Stopping old app..."
                    pkill -9 -f gunicorn || true
                    pkill -9 -f app.py || true
                    sleep 2

                    echo "Starting new app..."
                    nohup $VENV_DIR/bin/gunicorn -w 2 -b 0.0.0.0:$FLASK_PORT app:app > app.log 2>&1 &

                    sleep 3

                    echo "Check running process:"
                    ps aux | grep gunicorn
                '''
            }
        }

        stage('Verify App') {
            steps {
                sh '''
                    curl -I http://localhost:$FLASK_PORT || true
                '''
            }
        }
    }
}