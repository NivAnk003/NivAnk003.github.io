pipeline {
  agent any

  environment {
    AWS_REGION = 'ap-south-1' 
    ECR_REPO = '235494802089.dkr.ecr.ap-south-1.amazonaws.com/lambda-image'
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
                        aws configure set default.region ap-south-1

                        aws ecr get-login-password --region $AWS_DEFAULT_REGION | docker login --username AWS --password-stdin $ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com/$IMAGE_NAME
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
        docker tag $IMAGE_TAG $ECR_REPO
        docker push $ECR_REPO
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