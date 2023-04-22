pipeline {
  agent any

  environment{
    APP_NAME = "java-app-argo"
  }

  stages {

    stage('Clean Workspace'){
      steps{
        script{
          cleanWs()
        }
      }
    }

    stage('Checkout SCM'){
      steps{
        script{
          git credentialsId: 'Github',
          url: 'https://github.com/Taiwolawal/DevSecOps-CD.git',
          branch: 'main'
        }
      }
    }

    /* stage('Vulnerability Scan'){
      steps{
        parallel(
          "Dependency Scan":{
            sh "mvn dependency-check:check"
          }, 
          "Dockerfile Scan":{
            script {
              sh "trivy config ."
              sh "bash trivy-dockerfile-image-scan.sh"
              sh "trivy fs Dockerfile"
            }
          }
        )      
      }
    }   */
   
    stage ('Update Kubernetes Deployment File'){
      steps{
        script{
          sh """
            cat deployment.yaml
            sed -i 's/${APP_NAME}.*/:${APP_NAME}:${IMAGE_TAG}/g' deployment.yaml
            cat deployment.yaml
          """
        }
      }
    }

    stage('Push Changed Deployment File to Git'){
      steps{
        script{
          sh """
            git config --global user.name "Taiwolawal"
            git config --global user.email "taiwolawal360@gmail.com"
            git add deployment.yaml
            git commit -m "updated deployment file"
          """
          withCredentials([gitUsernamePassword(credentialsId: 'Github', gitToolName: 'Default')]) {
            sh "git push https://github.com/Taiwolawal/DevSecOps-CD.git main"
          }         
        }
        }
    } 

  }

 
 } 


