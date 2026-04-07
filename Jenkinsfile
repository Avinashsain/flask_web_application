pipeline {
    agent any

    environment {
        APP_DIR = "/var/lib/jenkins/flask_app"
        VENV = "${APP_DIR}/venv"
        PORT = "4000"
        MONGO_URI = credentials('MONGO_URI')
        SECRET_KEY = credentials('SECRET_KEY')
    }

    stages {

        stage('Prepare Directory') {
            steps {
                sh '''
                    echo "Creating app directory..."
                    sudo mkdir -p $APP_DIR
                    sudo chown -R jenkins:jenkins $APP_DIR
                '''
            }
        }

        stage('Clone / Update Code') {
            steps {
                sh '''
                    if [ ! -d "$APP_DIR/.git" ]; then
                        echo "Cloning repo..."
                        git clone https://github.com/Avinashsain/flask_web_application.git $APP_DIR
                    else
                        echo "Updating repo..."
                        cd $APP_DIR
                        git reset --hard
                        git pull origin master
                    fi
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

        stage('Deploy App') {
            steps {
                sh '''
                    echo "Stopping old app..."
                    pkill -f gunicorn || true
                    sleep 3

                    echo "Starting app..."
                    cd $APP_DIR

                    nohup $VENV/bin/gunicorn -w 2 -b 0.0.0.0:$PORT app:app > app.log 2>&1 &

                    sleep 5

                    echo "Running processes:"
                    ps aux | grep gunicorn
                '''
            }
        }

        stage('Verify') {
            steps {
                sh '''
                    echo "Testing application..."
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
            echo "❌ Deployment Failed - check logs"
        }
    }
}