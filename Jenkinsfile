pipeline {
    agent any

    environment {
        NOTIFY_EMAIL = 'pathakvivek86252@gmail.com'
        STAGING_DIR  = "${WORKSPACE}/../flask_staging"
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
                    mkdir -p "$STAGING_DIR"
                    cp -r app.py requirements.txt "$STAGING_DIR"/
                    echo "Deployed to staging: $STAGING_DIR"
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
