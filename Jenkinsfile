pipeline {
    agent any

    environment {
        APP_DIR = "${WORKSPACE}"
        VENV_DIR = "${WORKSPACE}/venv"
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
                // Use bash explicitly to avoid 'source' errors
                sh '''
                bash -c "
                cd $APP_DIR
                # Create virtual environment if not exists
                python3 -m venv venv || true
                # Upgrade pip inside venv
                $VENV_DIR/bin/pip install --upgrade pip
                # Install dependencies inside venv
                $VENV_DIR/bin/pip install -r requirements.txt
                "
                '''
            }
        }

        stage('Deploy Flask App') {
            steps {
                echo "Deploying Flask app..."
                sh '''
                bash -c "
                cd $APP_DIR
                # Kill old Flask process if exists
                pkill -f app.py || true
                sleep 2
                # Run Flask app in background using venv python
                nohup $VENV_DIR/bin/python app.py > app.log 2>&1 &
                "
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