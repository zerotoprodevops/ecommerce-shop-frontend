pipeline{

    agent {labels 'build-vm'}
    environment{
        IMAGE = 'jyotimanab/ecommerce-shop-frontend'
        TAG = 'v1'
    }

    stages{
        stage('Git Checkout'){
            steps{
                git(url: 'https://github.com/zerotoprodevops/ecommerce-shop-frontend.git', branch: 'main')    
            }

        }
        stage('Build'){
            steps{
                sh"""
                    docker build -t $IMAGE:$TAG .
                """
            }
        }
        stage('Scan Image'){
            steps{
                sh "TMPDIR=/tempdir trivy image --severity HIGH,CRITICAL --exit-code 1 $IMAGE:$TAG"
            }
        }
    }
}