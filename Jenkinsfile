pipeline {
    agent any

    environment {
        PROJECT_ID = 'bunny-project-444905'
        GOOGLE_APPLICATION_CREDENTIALS = credentials('test-gcp-appengine') // Service account credential
    }

    stages {
        stage('Prepare Environment') {
            steps {
                script {
                    // Install required system packages
                    sh '''
                    sudo apt-get update
                    sudo apt-get install -y python3.11-venv
                    '''
                }
            }
        }

        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/GollaGiddaiah/appengine-2025.git'
            }
        }

        stage('Set Up Virtual Environment') {
            steps {
                script {
                    // Create and activate a virtual environment
                    sh '''
                    python3 -m venv venv
                    source venv/bin/activate
                    pip install --upgrade pip
                    '''
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    // Install dependencies
                    sh '''
                    source venv/bin/activate
                    pip install -r requirements.txt
                    '''
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    // Run tests
                    sh '''
                    source venv/bin/activate
                    pytest
                    '''
                }
            }
        }

        stage('Deploy to Google App Engine') {
            steps {
                script {
                    // Authenticate with Google Cloud
                    sh '''
                    gcloud auth activate-service-account --key-file=${GOOGLE_APPLICATION_CREDENTIALS}
                    gcloud config set project $PROJECT_ID
                    '''

                    // Deploy to App Engine
                    sh '''
                    gcloud app deploy --quiet
                    '''
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
        }

        success {
            echo 'Deployment successful!'
        }

        failure {
            echo 'Deployment failed!'
        }
    }
}
