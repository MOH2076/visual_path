pipeline {
  agent any
  environment {
    AWS_REGION = 'ap-south-1'
    ACCOUNT_ID =  471112598325
    ECR_REPO = 471112598325.dkr.ecr.ap-south-1.amazonaws.com/visual_path_app
    IMAGE_TAG = "${env.BUILD_NUMBER}" // versioned
    LATEST_TAG = "latest"             // always latest
    CLUSTER = "VisualPathCluster"
    SERVICE = "VisualPathService"
  }
  stages {
    stage('Checkout') {
      steps { checkout scm }
    }
    stage('Build Docker') {
      steps {
        sh """
          docker build -t visual-path-app:${IMAGE_TAG} .
          docker tag visual-path-app:${IMAGE_TAG} ${ECR_REPO}:${IMAGE_TAG}
          docker tag visual-path-app:${IMAGE_TAG} ${ECR_REPO}:${LATEST_TAG}
        """
      }
    }
    stage('ECR login & Push') {
      steps {
        sh """
          aws ecr get-login-password --region ${AWS_REGION} | \
          docker login --username AWS --password-stdin ${ECR_REPO}

          aws ecr describe-repositories --repository-names visual_path_app || \
            aws ecr create-repository --repository-name visual_path_app

          docker push ${ECR_REPO}:${IMAGE_TAG}
          docker push ${ECR_REPO}:${LATEST_TAG}
        """
      }
    }
    stage('Register TaskDef and Deploy') {
      steps {
        sh """
          IMAGE_URI=${ECR_REPO}:${IMAGE_TAG}
          sed "s|<IMAGE>|${IMAGE_URI}|g" taskdef.json.template > taskdef.json
          ARN=$(aws ecs register-task-definition --cli-input-json file://taskdef.json --query 'taskDefinition.taskDefinitionArn' --output text)
          aws ecs update-service --cluster ${CLUSTER} --service ${SERVICE} --task-definition $ARN --force-new-deployment --region ${AWS_REGION}
        """
      }
    }
  }
  post {
    success { echo "Deployment successful: ${ECR_REPO}:${IMAGE_TAG} and ${ECR_REPO}:${LATEST_TAG}" }
    failure { echo "Deployment FAILED" }
  }
}
