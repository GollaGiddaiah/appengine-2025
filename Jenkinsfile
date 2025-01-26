pipeline {
    agent any

    environment {
        PROJECT_ID = 'bunny-project-444905'
        GOOGLE_APPLICATION_CREDENTIALS = credentials('test-gcp-appengine') // Service account credential
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/GollaGiddaiah/appengine-2025.git'
            }
        }

        stage('Set Up Environment') {
            steps {
                script {
                    // Install Poetry and set up a virtual environment
                    sh '''
                    curl -sS https://install.python-poetry.org | python3 -
                    export PATH="$HOME/.local/bin:$PATH"
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
                    // Install dependencies using Poetry
                    sh '''
                    source venv/bin/activate
                    poetry install
                    '''
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    // Run your tests using the virtual environment
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
