pipeline {
    agent any

    environment {
        APP_DIR = "/home/ubuntu/flask_app"
        PORT = "4000"
        VENV = "${APP_DIR}/venv"
        MONGO_URI = credentials('MONGO_URI')
        SECRET_KEY = credentials('SECRET_KEY')
    }

    stages {

        stage('Clone Latest Code') {
            steps {
                sh '''
                    if [ ! -d "$APP_DIR" ]; then
                        git clone https://github.com/Avinashsain/flask_web_application.git $APP_DIR
                    fi

                    cd $APP_DIR
                    git reset --hard
                    git pull origin master
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    cd $APP_DIR

                    python3 -m venv $VENV || true
                    $VENV/bin/pip install --upgrade pip
                    $VENV/bin/pip install -r requirements.txt
                    $VENV/bin/pip install gunicorn
                '''
            }
        }

        stage('Restart App') {
            steps {
                sh '''
                    echo "Stopping old app safely..."
                    pkill -f gunicorn || true
                    sleep 3

                    echo "Starting new app..."
                    cd $APP_DIR

                    nohup $VENV/bin/gunicorn -w 2 -b 0.0.0.0:$PORT app:app > app.log 2>&1 &
                    sleep 5

                    ps aux | grep gunicorn
                '''
            }
        }

        stage('Verify') {
            steps {
                sh '''
                    curl -I http://localhost:$PORT || true
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Deployment Successful"
        }
        failure {
            echo "❌ Deployment Failed"
        }
    }
}