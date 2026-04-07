pipeline {
    agent any

    environment {
        APP_DIR = "/home/ubuntu/flask_web_application"
        VENV = "${APP_DIR}/venv"
        PORT = "4000"

        MONGO_URI = credentials('MONGO_URI')
        SECRET_KEY = credentials('SECRET_KEY')
    }

    stages {

        stage('Prepare Directory') {
            steps {
                sh '''
                    echo "📁 Creating app directory..."
                    mkdir -p $APP_DIR
                '''
            }
        }

        stage('Clone / Update Code') {
            steps {
                sh '''
                    if [ ! -d "$APP_DIR/.git" ]; then
                        echo "📥 Cloning repo..."
                        git clone https://github.com/Avinashsain/flask_web_application.git $APP_DIR
                    else
                        echo "🔄 Updating repo..."
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

                    python3 -m venv venv || true
                    $VENV/bin/pip install --upgrade pip
                    $VENV/bin/pip install -r requirements.txt
                    $VENV/bin/pip install gunicorn
                '''
            }
        }

        stage('Deploy App') {
            steps {
                sh '''
                    echo "🛑 Stopping old app..."
                    pkill -9 -f gunicorn || true
                    pkill -9 -f app.py || true
                    sleep 2

                    echo "🚀 Starting app..."
                    cd $APP_DIR

                    nohup $VENV/bin/gunicorn \
                        -w 2 \
                        -b 0.0.0.0:$PORT \
                        app:app > app.log 2>&1 &

                    sleep 5

                    echo "📌 Running processes:"
                    ps aux | grep gunicorn
                '''
            }
        }

        stage('Verify') {
            steps {
                sh '''
                    echo "🌐 Testing app..."
                    curl -I http://localhost:$PORT || true

                    echo "📦 Latest commit:"
                    cd $APP_DIR
                    git log -1
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Deployment Successful - Latest code live"
        }
        failure {
            echo "❌ Deployment Failed - Check logs"
        }
    }
}