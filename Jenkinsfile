pipeline {
    agent any

    environment {
        APP_DIR = "/root/flask_web_application"
    }

    stages {

        stage('Clone') {
            steps {
                echo "Cloning repo..."
                git 'https://github.com/Avinashsain/flask_web_application.git'
            }
        }

        stage('Setup Python Env') {
            steps {
                echo "Setting up virtual environment..."
                sh '''
                cd $APP_DIR

                # Install venv if missing
                sudo apt update
                sudo apt install -y python3-venv python3-pip

                # Create venv if not exists
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
                echo "Deploying Flask app..."
                sh '''
                cd $APP_DIR

                # Kill old Flask app if running
                pkill -f app.py || true
                sleep 2

                # Activate virtual environment
                source venv/bin/activate

                # Start app in background (port 4000)
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