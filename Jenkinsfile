pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'               
        S3_BUCKET = 'riteshkart-static-website'  
    }

    stages {
        stage('Checkout Source Code') {
            steps {
                git branch: 'main', url: 'https://github.com/Ritesh1914/static-website-hosting-jenkins'
            }
        }

        stage('Install AWS CLI') {
            steps {
                sh '''
                if ! command -v aws &> /dev/null; then
                    echo "Installing AWS CLI..."
                    curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
                    unzip awscliv2.zip
                    sudo ./aws/install
                else
                    echo "AWS CLI is already installed."
                fi
                '''
            }
        }

        stage('Deploy to S3') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'AWS S3 Credentials' 
                ]]) {
                    sh '''
                    aws s3 sync . s3://$S3_BUCKET --delete --acl public-read --exclude ".git/*" --region $AWS_REGION
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ Deployment to S3 succeeded.'
        }
        failure {
            echo '❌ Deployment failed.'
        }
    }
}
