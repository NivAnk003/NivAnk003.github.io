pipeline {
  agent any

  environment {
  AWS_REGION = 'ap-south-1' 
  ACCOUNT_ID = '235494802089'
  REPO_NAME = 'lambda-image'
  ECR_REPO = "${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${REPO_NAME}"
  IMAGE_TAG = "lambda-image:${BUILD_NUMBER}"
}


  stages {

    stage('Checkout Code') {
      steps {
        git branch: 'main',
            url: 'https://github.com/NivAnk003/NivAnk003.github.io.git'
      }
    }

    stage('Install Dependencies') {
      steps {
        sh 'pip install -r requirements.txt'
      }
    }

    stage('Run Unit Tests') {
      steps {
        echo 'pytest tests/' // optional
      }
    }

    stage('Authenticate to ECR') {
      steps {
                withCredentials([usernamePassword(credentialsId: 'aws-credentials', usernameVariable: 'AWS_ACCESS_KEY_ID', passwordVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                    sh '''
                        aws configure set aws_access_key_id $AWS_ACCESS_KEY_ID
                        aws configure set aws_secret_access_key $AWS_SECRET_ACCESS_KEY
                        aws configure set default.region $AWS_REGION

                        aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin ${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
                    '''
                }
            }
    }

    stage('Build Docker Image') {
      steps {
        sh "docker build -t $IMAGE_TAG ."
      }
    }

    stage('Tag & Push to ECR') {
      steps {
        sh """
        docker tag $IMAGE_TAG $ECR_REPO:$BUILD_NUMBER
        docker push $ECR_REPO:$BUILD_NUMBER
        """
      }
    }
  }

  post {
    success {
      echo "Docker image pushed to ECR: $ECR_REPO"
    }
    failure {
      echo "Build failed. Check logs."
    }
  }
}