pipeline {
  agent any

  environment{
    DOCKERHUB_USERNAME = "wizhubdocker8s"
    APP_NAME = "java-app-argo"
    IMAGE_NAME = "${DOCKERHUB_USERNAME}" + "/" + "${APP_NAME}"
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
    
    stage('K8s Authenitcation: Kubeconfig'){
      steps{
          withKubeConfig([credentialsId: 'kubeconfig']) {
            sh 'kubectl version --short'
          }
      }
    }

    stage('Vulnerability Scan - Kubernetes'){
        steps{
            withKubeConfig([credentialsId: 'kubeconfig']){
                sh "trivy k8s --timeout 10m --report summary cluster"
                //sh "trivy k8s -n kube-system --report summary all"    
                //sh "bash trivy-image-scan.sh"
            }
        }
    }

    stage ('Update Deployment File'){
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

    stage('Scan Deployment File'){
        steps{
            withKubeConfig([credentialsId: 'kubeconfig']){
                sh "trivy config deployment.yaml"
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


