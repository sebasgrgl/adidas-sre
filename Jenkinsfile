pipeline {
  agent any

  tools {
    maven 'maven'
    jdk 'jdk11'
    dockerTool 'docker'
  }

  stages {
    stage('Set Environment Variables') {
      steps {
        script {
          // echo "JOB_NAME = ${JOB_NAME}"
          // echo "BUILD_NUMBER = ${BUILD_NUMBER}"
          // echo "BUILD_URL = ${BUILD_URL}"
          // echo "GIT_URL = ${GIT_URL}"
          // echo "BRANCH_NAME = ${BRANCH_NAME}"

          switch(BRANCH_NAME) {
            case "production":
              env.DOCKER_TAG = "production"
              env.PROJECT_ID = 'adidas-sre-311717'
              env.CLUSTER_NAME = 'production'
              break
            case "production":
              env.DOCKER_TAG = "staging"
              env.PROJECT_ID = 'adidas-sre-311717'
              env.CLUSTER_NAME = 'staging'
              break
            default:
              env.DOCKER_TAG = "development"
              env.PROJECT_ID = 'adidas-sre-311717'
              env.CLUSTER_NAME = 'development'
              break
          }
          env.NAMESPACE = "default"
          env.DOCKER_IMAGE_NAME = "adidas-sre-app"
          env.DOCKER_REGISTRY = "us.gcr.io/${PROJECT_ID}"

          //Print environment variables
          echo "DOCKER_TAG = ${DOCKER_TAG}"
          echo "PROJECT_ID = ${PROJECT_ID}"
          echo "CLUSTER_NAME = ${CLUSTER_NAME}"
          echo "NAMESPACE = ${NAMESPACE}"
          echo "DOCKER_REGISTRY = ${DOCKER_REGISTRY}"
          echo "DOCKER_IMAGE_NAME = ${DOCKER_IMAGE_NAME}"
        }
      }
    }
    stage('Build') {
      steps {
        sh 'mvn -B -DskipTests clean package'
      }
    }
    stage('Test') {
      steps {
        sh("mvn test")
      }
    }
    stage("Build Docker Image") {
      when {
        anyOf {
          branch 'development'
          branch 'staging'
          branch 'production'
        }
      }
      steps {
        sh ("/usr/local/bin/docker-credential-gcr configure-docker")
        echo "DOCKER_TAG = ${DOCKER_TAG}"
        sh("docker build -t ${DOCKER_REGISTRY}/${DOCKER_IMAGE_NAME}:${DOCKER_TAG} .")
        sh("docker push ${DOCKER_REGISTRY}/${DOCKER_IMAGE_NAME}:${DOCKER_TAG}")
      }
    }
  }



  //   stage('Make Container') {
  //     steps {
  //     sh "docker build -t snscaimito/ledger-service:${env.BUILD_ID} ."
  //     sh "docker tag snscaimito/ledger-service:${env.BUILD_ID} snscaimito/ledger-service:latest"
  //     }
  //   }
  //
  //   stage('Check Specification') {
  //     steps {
  //       sh "chmod o+w *"
  //       sh "docker-compose up --exit-code-from cucumber --build"
  //     }
  //   }
  // }

  // post {
  //   always {
  //     archive 'target/**/*.jar'
  //     junit 'target/**/*.xml'
  //     cucumber '**/*.json'
  //   }
  //   success {
  //     withCredentials([usernamePassword(credentialsId: 'docker-credentials', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
  //       sh "docker login -u ${USERNAME} -p ${PASSWORD}"
  //       sh "docker push snscaimito/ledger-service:${env.BUILD_ID}"
  //       sh "docker push snscaimito/ledger-service:latest"
  //     }
  //   }
  // }
}
