node{
//Define all variables
  def project = 'adidas-sre-311717'
  def appName = 'adidas-sre'
  def serviceName = "${appName}-backend"
  def imageVersion = 'development'
  def namespace = 'development'
  def imageTag = "gcr.io/${project}/${appName}:${imageVersion}.${env.BUILD_NUMBER}"
  def mvn_version = tool 'maven'
  def jdk_version = tool 'jdk11'

   // stage('Set Environment Variables') {
   //   steps {
   //     script {
   //       echo "JOB_NAME = ${JOB_NAME}"
   //       echo "BUILD_NUMBER = ${BUILD_NUMBER}"
   //       echo "BUILD_URL = ${BUILD_URL}"
   //       echo "GIT_URL = ${GIT_URL}"
   //       echo "BRANCH_NAME = ${BRANCH_NAME}"
   //
   //       switch(BRANCH_NAME) {
   //         case "production":
   //           env.DOCKER_TAG = "prod"
   //           env.PROJECT_ID = 'adidas-sre-311717'
   //           env.CLUSTER_NAME = 'production'
   //           break
   //         case "staging":
   //           env.DOCKER_TAG = "stg"
   //           env.PROJECT_ID = 'adidas-sre-311717'
   //           env.CLUSTER_NAME = 'staging'
   //           break
   //         default:
   //           env.DOCKER_TAG = "dev"
   //           env.PROJECT_ID = 'adidas-sre-311717'
   //           env.CLUSTER_NAME = 'development'
   //           break
   //       }
   //       env.NAMESPACE = "default"
   //       env.DOCKER_IMAGE_NAME = "adidas-sre"
   //       env.DOCKER_REGISTRY = "us.gcr.io/${PROJECT_ID}"
   //
   //       //Print environment variables
   //       echo "DOCKER_TAG = ${DOCKER_TAG}"
   //       echo "PROJECT_ID = ${PROJECT_ID}"
   //       echo "CLUSTER_NAME = ${CLUSTER_NAME}"
   //       echo "NAMESPACE = ${NAMESPACE}"
   //       echo "DOCKER_REGISTRY = ${DOCKER_REGISTRY}"
   //       echo "DOCKER_IMAGE_NAME = ${DOCKER_IMAGE_NAME}"
   //     }
   //   }
   // }
  // tools {
  //       maven 'maven'
  //       jdk 'jdk11'
  // }
  stage ('Initialize') {
    sh '''
    echo "PATH = ${PATH}"
    mvn --version
    '''
  }
  stage('Build Package') {
    sh("mvn -B -DskipTests clean package")
  }
  stage('Test Package') {
    sh("mvn test")
  }

}
