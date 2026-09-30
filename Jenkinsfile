pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Stop current deployment') {
            steps {
                sh '''
                    sudo -n -H -u DevOps /usr/bin/pm2 stop react-app || true
                '''
            }
        }

        stage('Deploy Dist') {
            steps {
                sh '''
                    rm -rf /opt/deployment/react/*
                    cp -r dist/. /opt/deployment/react/
                '''
            }
        }

        stage('Restart PM2') {
            steps {
                sh '''
                    sudo -n -H -u DevOps /usr/bin/pm2 restart react-app
                '''
            }
        }

        stage('Upload to S3') {
            steps {
                withCredentials([
                    [$class: 'AmazonWebServicesCredentialsBinding',
                     credentialsId: 'aws-s3']
                ]) {
                    sh '''
                        aws s3 sync dist/ s3://saadat-react-artifacts-2026 --delete
                    '''
                }
            }
        }
    }
}
