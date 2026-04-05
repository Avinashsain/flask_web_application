pipeline {
    agent any

    environment {
        APP_DIR = "${WORKSPACE}"
        VENV_DIR = "${WORKSPACE}/venv"
    }

    stages {

        stage('Clone Repository') {
            steps {
                git 'https://github.com/Avinashsain/flask_web_application.git'
            }
        }

        stage('Setup Python Environment') {
            steps {
                sh '''
                bash -c "
                cd $APP_DIR
                python3 -m venv venv || true
                $VENV_DIR/bin/pip install --upgrade pip
                $VENV_DIR/bin/pip install -r requirements.txt
                "
                '''
            }
        }

        stage('Deploy Flask App') {
            steps {
                sh '''
                bash -c "
                cd $APP_DIR
                # Kill old Flask process
                pkill -f app.py || true
                sleep 2
                # Start Flask in background and detach properly
                nohup $VENV_DIR/bin/python app.py > app.log 2>&1 &
                disown
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