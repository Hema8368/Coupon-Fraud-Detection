pipeline {
  agent any
  options { timestamps() }

  parameters {
    string(name: 'IMAGE_TAG', defaultValue: 'latest', description: 'Docker image tag to deploy (latest, v1, 12, etc.)')
  }

  environment {
    DEPLOY_HOST = "3.236.28.238"
    DEPLOY_USER = "ubuntu"
    COMPOSE_PATH = "/home/ubuntu/coupon-fraud"
  }

  stages {

    stage('Checkout Repo') {
      steps { checkout scm }
    }

    stage('Deploy to EC2') {
      steps {
        sshagent(credentials: ['deploy-ssh-key']) {
          sh """
            ssh -o StrictHostKeyChecking=no ${DEPLOY_USER}@${DEPLOY_HOST} 'mkdir -p ${COMPOSE_PATH}'
            scp -o StrictHostKeyChecking=no docker-compose.prod.yml ${DEPLOY_USER}@${DEPLOY_HOST}:${COMPOSE_PATH}/docker-compose.yml

            ssh -o StrictHostKeyChecking=no ${DEPLOY_USER}@${DEPLOY_HOST} '
              set -e
              cd ${COMPOSE_PATH}
              export TAG=${IMAGE_TAG}
              docker compose pull
              docker compose up -d
              docker compose ps
            '
          """
        }
      }
    }
  }
}
