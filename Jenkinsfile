  pipeline{
    agent any
    tools{
    maven 'maven'
    }
    stages{
//        stage('build maven'){
//           steps{
//               checkout scmGit(branches: [[name: '*/lms-jenkins-pipeline']], extensions: [], userRemoteConfigs: [[credentialsId: 'git_credential', url: 'https://github.com/apaspxp/lms-helmchart.git']])
//                sh 'mvn clean install'
//                }
//             }
//        stage('Database Connection'){
//           steps {
//             script {
//                 withCredentials([usernamePassword(credentialsId: 'mysql-credentials-id', usernameVariable: 'DB_USERNAME', passwordVariable: 'DB_PASSWORD')]){
//
//                 }
//             }
//           }
//        }
       stage('Build Docker Image') {
          steps {
            script {
               withCredentials([usernamePassword(credentialsId: 'DockerHub_Credential', passwordVariable: 'DOCKERHUB_PASSWORD', usernameVariable: 'DOCKERHUB_USERNAME')]) {
               // Generate a timestamp or version number for the image tag
               def timestamp = new Date().format("yyyyMMdd_HHmmss")
               def imageTag = "lms-config-server:${timestamp}"

              // Build the Docker image using the spring-boot:build-image Maven goal
               sh "mvn spring-boot:build-image -Dspring-boot.build-image.imageName=apaspxp/${imageTag}"
              //    sh "mvn spring-boot:build-image -Dspring-boot.build-image.imageName=apaspxp/lms-config-server:latest"

             // Log in to Docker Hub using the credentials
                sh "docker login -u $DOCKERHUB_USERNAME -p $DOCKERHUB_PASSWORD"
                // sh "echo $DOCKERHUB_PASSWORD | docker login --username $DOCKERHUB_USERNAME --password-stdin"

                // Push the Docker image to a registry
                sh "docker push apaspxp/${imageTag}"
                // sh "docker push apaspxp/lms-config-server:latest"

                // Tag the image as "latest"
                sh "docker tag apaspxp/${imageTag} apaspxp/lms-config-server:latest"
                sh "docker push apaspxp/lms-config-server:latest"

                // Print the image tag for reference
                echo "Docker image tag: ${imageTag}"
                }
              }
            }
        }
       stage('Deploy to Kubernetes'){
            steps{
                script {
                    withCredentials([file(credentialsId: 'Kubernetes_Credentials', variable: 'KUBECONFIG')]) {
                    //Cleanup the resources
                    sh "kubectl delete -f lms-config-service-service.yaml --ignore-not-found"
                    //Apply the new manifest file
                    sh "kubectl apply -f lms-config-service-service.yaml"
                   }
                }
            }
        }
     }
  }

