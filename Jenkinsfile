pipeline {
    agent any

    stages {

        stage('Clone') {
            steps {
                git 'https://github.com/Avinashsain/flask_web_application.git'
            }
        }

        stage('Setup') {
            steps {
                sh '''
                python3 -m venv venv || true
                . venv/bin/activate
                pip install -r requirements.txt
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                pkill -f app.py || true
                nohup venv/bin/python app.py > app.log 2>&1 &
                '''
            }
        }
    }
}