pipeline {
    agent any

    environment {
        APP_DIR = "/home/ubuntu/flask_web_application"
        PORT = "4000"
        MONGO_URI = credentials('MONGO_URI')
        SECRET_KEY = credentials('SECRET_KEY')
    }

    stages {

        stage('Clone / Update Code') {
            steps {
                sh '''
                    echo "Updating code..."

                    if [ ! -d "$APP_DIR/.git" ]; then
                        git clone https://github.com/Avinashsain/flask_web_application.git $APP_DIR
                    else
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

                    if [ ! -d "venv" ]; then
                        python3 -m venv venv
                    fi

                    ./venv/bin/pip install --upgrade pip
                    ./venv/bin/pip install -r requirements.txt
                    ./venv/bin/pip install gunicorn
                '''
            }
        }

        stage('Deploy App') {
            steps {
                sh '''
                    echo "Stopping old app..."
                    pkill -f "gunicorn.*$PORT" || true

                    sleep 2

                    echo "Starting app..."
                    cd $APP_DIR

                    nohup ./venv/bin/gunicorn -w 2 -b 0.0.0.0:$PORT app:app > app.log 2>&1 &

                    sleep 5

                    echo "Running processes:"
                    ps aux | grep gunicorn
                '''
            }
        }

        stage('Verify') {
            steps {
                sh '''
                    echo "Testing app..."
                    curl -f http://localhost:$PORT
                    curl -f http://13.203.223.150:$PORT

                    echo "Last logs:"
                    tail -n 20 $APP_DIR/app.log || true

                    echo "Latest commit:"
                    cd $APP_DIR
                    git log -1
                '''
            }
        }
    }

    post {
        success {
            echo "Deployment Successful - Latest code live"
        }
        failure {
            echo "Deployment Failed - Check logs"
        }
    }
}