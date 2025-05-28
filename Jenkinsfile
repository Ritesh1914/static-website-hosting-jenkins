 pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        S3_BUCKET = 'riteshkart-static-website'
    }

    stages {
        stage('Checkout Source Code') {
            steps {
                git 'https://github.com/Ritesh1914/static-website-hosting-jenkins'
            }
        }

        stage('Check AWS CLI Installation') {
            steps {
                bat '''
                where aws >nul 2>nul
                if %ERRORLEVEL% NEQ 0 (
                    echo AWS CLI is not installed. Please install it manually from https://aws.amazon.com/cli/
                    exit 1
                ) else (
                    aws --version
                )
                '''
            }
        }

        stage('Deploy to S3') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding',
                                  credentialsId: 'aws-credentials']]) {
                    bat '''
                    echo Deploying to S3 bucket: %S3_BUCKET% in region %AWS_REGION%
                    aws s3 sync . s3://%S3_BUCKET% --delete --exclude ".git/*" --region %AWS_REGION%
                    '''
                }
            }
        }
    }

    post {
        failure {
            echo '❌ Deployment failed.'
        }
        success {
            echo '✅ Deployment successful!'
        }
    }
}
