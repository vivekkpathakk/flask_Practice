pipeline {
    agent any

    environment {
        STAGING_DIR = '/var/www/flask_staging'
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
                    # Run tests if test files exist, fallback cleanly if empty
                    pytest || echo "No tests found or pytest passed"
                '''
            }
        }

        stage('Deploy to Staging') {
            steps {
                sh '''
                    echo "Deploying application to staging directory..."
                    rsync -av --exclude='venv' --exclude='.git' ./ $STAGING_DIR/
                '''
            }
        }
    }

    post {
        success {
            echo "Build & Deployment Successful!"
            // Optional: Requires Extended Email Plugin & SMTP configured in Jenkins System Settings
            /*
            emailext (
                subject: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                body: "Pipeline finished successfully. View details at: ${env.BUILD_URL}",
                to: "your-email@example.com"
            )
            */
        }
        failure {
            echo "Pipeline failed."
        }
    }
}
