pipeline {
    agent any

    environment {
        VENV = "${WORKSPACE}/venv"
        PORT = "4000"
        MONGO_URI = credentials('MONGO_URI')
        SECRET_KEY = credentials('SECRET_KEY')
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
                    echo "Mongo URI: $MONGO_URI"
                    echo "SECRET KEY: $SECRET_KEY"
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