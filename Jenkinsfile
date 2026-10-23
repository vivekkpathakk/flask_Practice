pipeline {
    agent any

    environment {
        STAGING_DIR = '/var/www/flask_staging'
        NOTIFY_EMAIL = 'pathakvivek86252@gmail.com'
    }

    stages {
        stage('Build') {
            steps {
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install --upgrade pip
                    if [ -f requirements.txt ]; then pip install -r requirements.txt; fi
                    pip install pytest
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                    . venv/bin/activate
                    pytest -v
                '''
            }
        }

        stage('Deploy to Staging') {
            steps {
                sh '''
                    echo "Deploying application to staging directory..."
                    mkdir -p $STAGING_DIR
                    rsync -av --exclude='venv' --exclude='.git' ./ $STAGING_DIR/
                '''
            }
        }
    }

    post {
        success {
            emailext(
                subject: "SUCCESS: Job '${env.JOB_NAME}' [Build #${env.BUILD_NUMBER}]",
                body: """Pipeline finished successfully.

Job:     ${env.JOB_NAME}
Build:   #${env.BUILD_NUMBER}
Status:  SUCCESS
Details: ${env.BUILD_URL}""",
                to: "${env.NOTIFY_EMAIL}"
            )
        }
        failure {
            emailext(
                subject: "FAILED: Job '${env.JOB_NAME}' [Build #${env.BUILD_NUMBER}]",
                body: """Pipeline failed. Check the console output.

Job:     ${env.JOB_NAME}
Build:   #${env.BUILD_NUMBER}
Status:  FAILURE
Details: ${env.BUILD_URL}console""",
                to: "${env.NOTIFY_EMAIL}"
            )
        }
    }
}
