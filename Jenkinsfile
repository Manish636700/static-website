pipeline {
    agent any

    environment {
        AWS_ACCESS_KEY_ID     = credentials('aws-access-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-access-key')
        AWS_DEFAULT_REGION    = 'us-east-1'
        S3_BUCKET             = 'my-static-website-manish'
    }

    stages {
        stage('Clone Code') {
            steps {
                git 'https://github.com/Manish636700/static-website.git'
            }
        }

        stage('Deploy to S3') {
            steps {
                sh '''
                echo "Deploying site to S3..."
                aws s3 sync . s3://$S3_BUCKET --delete
                '''
            }
        }
    }
}
