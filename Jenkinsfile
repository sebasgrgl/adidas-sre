node{
//Define all variables
  def project = 'adidas-sre-311717'
  def appName = 'adidas-sre'
  def serviceName = "${appName}-backend"

  //Checkout Code from Git
     checkout scm

   stage('Set Environment Variables') {
     steps {
       script {
         echo "JOB_NAME = ${JOB_NAME}"
         echo "BUILD_NUMBER = ${BUILD_NUMBER}"
         echo "BUILD_URL = ${BUILD_URL}"
         echo "GIT_URL = ${GIT_URL}"
         echo "BRANCH_NAME = ${BRANCH_NAME}"

         switch(BRANCH_NAME) {
           case "production":
             env.DOCKER_TAG = "prod"
             env.PROJECT_ID = 'adidas-sre-311717'
             env.CLUSTER_NAME = 'production'
             break
           case "staging":
             env.DOCKER_TAG = "stg"
             env.PROJECT_ID = 'adidas-sre-311717'
             env.CLUSTER_NAME = 'staging'
             break
           default:
             env.DOCKER_TAG = "dev"
             env.PROJECT_ID = 'adidas-sre-311717'
             env.CLUSTER_NAME = 'development'
             break
         }
         env.NAMESPACE = "default"
         env.DOCKER_IMAGE_NAME = "adidas-sre"
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

  stage('Build Package') {
    steps {
      sh("mvn -B -DskipTests clean package -U")
    }
  }
  stage('Test Package') {
    steps {
      sh("mvn test")
    }
  }
  //Stage 1 : Build the docker imagetag.
  stage("Build Docker Image") {
    when {
      anyOf {
        branch 'production'
        branch 'staging'
        branch 'development'
      }
    }
    steps {
      sh ("docker-credential-gcr configure-docker")
      echo "DOCKER_TAG = ${DOCKER_TAG}"
      sh("docker build --build-arg JAR_PATH=\"`ls ./target/*.jar | grep -v sources | grep -v original`\" -t ${DOCKER_REGISTRY}/${DOCKER_IMAGE_NAME}:${DOCKER_TAG}-${BUILD_NUMBER} .")
      sh("docker push ${DOCKER_REGISTRY}/${DOCKER_IMAGE_NAME}:${DOCKER_TAG}-${BUILD_NUMBER}")
    }
  }

  // stage('Build image') {
  //     // def customImage = docker.build("my-image:${imageTag}")
	//   def dockerhome = tool 'docker'
	//   sh("docker build -t ${imageTag} .")
  // }

  //Stage 2 : Push the image to docker registry
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
        sh("kubectl config use-context ${CLUSTER_NAME}")

        // change image
        sh("")
        sh ("kubectl set image --record deployment/${DOCKER_IMAGE_NAME} ${DOCKER_IMAGE_NAME}=${DOCKER_REGISTRY}/${DOCKER_IMAGE_NAME}:${DOCKER_TAG}-${BUILD_NUMBER} --namespace ${NAMESPACE}")
      }
    }
  }
}
