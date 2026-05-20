pipeline {
    agent any

    environment {
        IMAGE_NAME = "python-project-2"
        DOCKER_HUB = "pradeep211031"
        BUILD_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Install & Test') {
            steps {
                sh '''
                python3 -m venv venv
                . venv/bin/activate

                pip install --upgrade pip
                pip install -r app/requirements.txt

                export PYTHONPATH=$PWD
                pytest tests/
                '''
            }
        }

        stage('Build & Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker-creds',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {

                    sh '''
                    echo "$PASS" | docker login -u "$USER" --password-stdin

                    docker build -t $DOCKER_HUB/$IMAGE_NAME:$BUILD_TAG app/

                    docker tag $DOCKER_HUB/$IMAGE_NAME:$BUILD_TAG \
                    $DOCKER_HUB/$IMAGE_NAME:latest

                    docker push $DOCKER_HUB/$IMAGE_NAME:$BUILD_TAG

                    docker push $DOCKER_HUB/$IMAGE_NAME:latest
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                echo "🚀 Deploy step completed"
            }
        }
    }

    post {
        success {
            echo "✅ Success: $DOCKER_HUB/$IMAGE_NAME:$BUILD_TAG"
        }

        failure {
            echo "❌ Failed"
        }
    }
}
