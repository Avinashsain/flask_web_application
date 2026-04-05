pipeline {
    agent any

    environment {
        APP_DIR = "${WORKSPACE}"  // Use Jenkins workspace
    }

    stages {

        stage('Clone Repository') {
            steps {
                echo "Cloning repo..."
                git 'https://github.com/Avinashsain/flask_web_application.git'
            }
        }

        stage('Setup Python Environment') {
            steps {
                echo "Creating virtual environment and installing dependencies..."
                sh '''
                cd $APP_DIR

                # Create venv if it doesn't exist
                python3 -m venv venv || true

                # Activate venv
                source venv/bin/activate

                # Upgrade pip
                pip install --upgrade pip

                # Install requirements inside venv
                pip install -r requirements.txt
                '''
            }
        }

        stage('Deploy Flask App') {
            steps {
                echo "Deploying Flask app..."
                sh '''
                cd $APP_DIR

                # Kill old Flask process if exists
                pkill -f app.py || true
                sleep 2

                # Activate virtual environment
                source venv/bin/activate

                # Start Flask app in background (port 4000)
                nohup python3 app.py > app.log 2>&1 &
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