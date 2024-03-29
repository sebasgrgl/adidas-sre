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
              env.CLUSTER_NAME = 'prod'
              break
            case "staging":
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
        sh("docker build -t ${DOCKER_REGISTRY}/${DOCKER_IMAGE_NAME}:${DOCKER_TAG}-${BUILD_NUMBER} .")
        sh("docker push ${DOCKER_REGISTRY}/${DOCKER_IMAGE_NAME}:${DOCKER_TAG}-${BUILD_NUMBER}")
      }
    }

    stage("K8s Deploy") {
      when {
        anyOf {
          branch 'production'
          branch 'staging'
          branch 'development'
        }
      }
      steps {
        script {
          sh ("docker-credential-gcr configure-docker")
          // set production image label
          sh("docker tag ${DOCKER_REGISTRY}/${DOCKER_IMAGE_NAME}:${DOCKER_TAG}-${BUILD_NUMBER} ${DOCKER_REGISTRY}/${DOCKER_IMAGE_NAME}:${DOCKER_TAG}")
          sh("docker push ${DOCKER_REGISTRY}/${DOCKER_IMAGE_NAME}:${DOCKER_TAG}")

          // Change context
          sh ("gcloud container clusters get-credentials ${CLUSTER_NAME} --zone us-east1-d --project ${PROJECT_ID}")
          sh ("kubectl config use-context gke_${PROJECT_ID}_us-east1-d_${CLUSTER_NAME}")

          // change image
          sh("")
          sh ("kubectl set image --record deployment/${DOCKER_IMAGE_NAME} ${DOCKER_IMAGE_NAME}=${DOCKER_REGISTRY}/${DOCKER_IMAGE_NAME}:${DOCKER_TAG}-${BUILD_NUMBER} --namespace ${NAMESPACE}")
        }
      }
    }
  }
}
