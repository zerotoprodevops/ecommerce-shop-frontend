pipeline{

    agent {label 'build-vm'}
    environment{
        IMAGE = 'jyotimanab/ecommerce-shop-frontend'
        TAG = "${env.BUILD_NUMBER}"
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
        stage("upload to docker hub"){
            steps{
                withCredentials([usernamePassword(credentialsId: 'dockerhub-cred', passwordVariable: 'DOCKER_PASS', usernameVariable: 'DOCKER_USER')]) {
                 sh"""
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    docker push $IMAGE:$TAG
                    docker tag $IMAGE:$TAG $IMAGE:latest
                    docker push $IMAGE:latest
                    """
                }
                
            }
        }
    }
    post{
        success{
            echo "Build $TAG Successful - Image pushed to DockerHub"
            sh "docker rmi $IMAGE:$TAG $IMAGE:latest" || echo 'cleanup: Image not found locally'
        }
        failure{
            echo "Pipeline failed - Image $IMAGE:$TAG kept locally for debugging"
        }
    }
}