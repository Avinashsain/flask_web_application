pipeline {
    agent any
    environment {
        APP_DIR = "${WORKSPACE}"
        VENV_DIR = "${WORKSPACE}/venv"
        FLASK_PORT = "4000"  // Change port if needed
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo "Cloning repo..."
                git branch: 'master', url: 'https://github.com/Avinashsain/flask_web_application.git'
            }
        }

        stage('Setup Python Environment') {
            steps {
                echo "Setting up virtual environment..."
                sh '''
                    python3 -m venv $VENV_DIR || true
                    $VENV_DIR/bin/pip install --upgrade pip
                    $VENV_DIR/bin/pip install -r requirements.txt
                '''
            }
        }

        stage('Deploy Flask App') {
            steps {
                echo "Deploying Flask app..."
                sh '''
                    cd $APP_DIR
                    git pull origin master

                    # Install dependencies directly in venv
                    $VENV_DIR/bin/pip install -r requirements.txt || true
                    $VENV_DIR/bin/pip install gunicorn || true

                    # Kill old Gunicorn process
                    pkill -f gunicorn || true
                    sleep 2

                    # Start Flask app
                    setsid $VENV_DIR/bin/gunicorn -w 4 -b 0.0.0.0:$FLASK_PORT app:app > app.log 2>&1 < /dev/null &
                '''
            }
        }
    }

    post {
        success {
            echo "Deployment Successful 🚀 Your app should be live on port $FLASK_PORT"
        }
        failure {
            echo "Deployment Failed ❌ Check app.log for details"
        }
    }
}