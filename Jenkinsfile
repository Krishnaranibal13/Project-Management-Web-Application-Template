
pipeline {

    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Verify Workspace') {
            steps {
                sh '''
                    echo "Jenkins workspace:"
                    pwd

                    echo "Project files:"
                    ls -la
                '''
            }
        }

        stage('Prepare Environment') {
            steps {
                sh '''
                    echo "Copying production environment file..."

                    cp /opt/project-management/.env .env

                    chmod 600 .env

                    echo ".env file prepared successfully."
                '''
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                    docker compose build --no-cache
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker compose up -d
                '''
            }
        }

        stage('Check Containers') {
            steps {
                sh '''
                    docker compose ps
                '''
            }
        }

        stage('Health Check') {
            steps {
                sh '''
                    echo "Waiting for application..."
                    sleep 10

                    echo "Checking API health..."

                    curl -f http://localhost/api/health
                '''
            }
        }
    }

    post {

        success {
            echo 'Project Management application deployed successfully!'
        }

        failure {
            echo 'Deployment failed.'

            sh '''
                docker compose ps || true
                docker compose logs --tail=100 || true
            '''
        }
    }
}



