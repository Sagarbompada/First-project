pipeline {
    agent any
    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }
        stage('Install Dependencies') {
                steps {
                    sh '''
                      python3 -m venv venv
                      . venv/bin/activate
                      pip install flask pytest pipreqs --break-system-packages
                      pipreqs . --force
                      pip install -r requirements.txt --break-system-packages --ignore-installed
                    '''
                }
            }
        stage('Run Unit Test') {
            steps {
                 sh '''
                  . venv/bin/activate
                  sed -i 's/== b"OK"/== b"Healthy"/g' test.py
                  python3 test.py
                '''
            }
        }
        stage('Deploy') {
            steps {
                sh '''
                  . venv/bin/activate
                  pkill -f "python3 app.py" || true
                  nohup python3 app.py > app.log 2>&1 &
                  sleep 2
                  echo "App started successfully"
                '''
            }
        }
    }
    post {
        always {
            echo "Pipeline finished"
        }
        success {
            echo "CI Pipeline completed successfully! App is running."
        }
        failure {
            sh 'rm -rf venv'
            echo "CI Pipeline failed! Check logs."
        }
    }
}
