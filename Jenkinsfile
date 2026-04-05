pipeline {
    agent any

    environment {
        APP_DIR = "${WORKSPACE}"
    }

    stages {

        stage('Clone') {
            steps {
                git 'https://github.com/Avinashsain/flask_web_application.git'
            }
        }

        stage('Setup Python Environment') {
            steps {
                sh '''
                cd $APP_DIR

                # Create virtual environment (no sudo)
                python3 -m venv venv || true

                # Activate venv
                source venv/bin/activate

                # Upgrade pip
                pip install --upgrade pip

                # Install dependencies
                pip install -r requirements.txt
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                cd $APP_DIR

                # Kill old process
                pkill -f app.py || true
                sleep 2

                # Activate venv
                source venv/bin/activate

                # Start Flask app in background
                nohup python3 app.py > app.log 2>&1 &
                '''
            }
        }
    }

    post {
        success { echo "Deployment Successful 🚀" }
        failure { echo "Deployment Failed ❌ Check app.log for details" }
    }
}