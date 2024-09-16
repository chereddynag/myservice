pipeline{
    agent any 
    options{
        buildDiscarder(logRotator(numToKeepStr: '3'))
    }
    environment{
        IMAGE_NAME = "${env.JOB_NAME}"
        GCR_REGION = "us-west1-docker.pkg.dev"
        GCR_IMAGE_URI = "${GCR_REGION}/${GCR_PROJECT_ID}/chereddy/${env.JOB_NAME}:${env.BUILD_NUMBER}"
        GOOGLE_APPLICATION_CREDENTIALS = credentials('mydevops')
        GCR_PROJECT_ID = 'fluted-volt-428205-p7'
        GKE_CLUSTER = 'my-k8-cluster'
        GKE_ZONE = 'us-west1-a'
        HELM_CHART_DIR = "myservice"
        HELM_REPO_NAME = "devops"
        HELM_REPO_URL = "oci://${GCR_REGION}/${GCR_PROJECT_ID}/helmrepo"
        DEPLOYMENT_NAME = "my-deployment"
        HELM_RELEASE_NAME = "Mysrevice"

    }
    stages{
        stage("Aauthenticate with GCP"){
            steps{
                script{
                    sh 'gcloud config set project ${GCR_PROJECT_ID}'
                    sh 'gcloud auth activate-service-account --key-file=/tmp/devops.json'
                    sh 'gcloud container clusters get-credentials ${GKE_CLUSTER} --zone ${GKE_ZONE} --project ${GCR_PROJECT_ID}'
                }
            }
            
        }
        stage("Creating Helm charts"){
            steps{
                script{
                    sh 'helm create ${HELM_CHART_DIR}'

                }
            }
        }
        stage("Create helm package"){
            steps{
                script{
                    sh 'helm package ${HELM_CHART_DIR}'
                }
            }
        }
        stage("push helm charts"){
            steps{
                script{
                    sh 'helm repo add ${HELM_REPO_NAME} ${HELM_REPO_URL}'
                    sh 'helm push ${HELM_CHART_DIR}/target/*.tgz ${HELM_REPO_NAME}'
                }
            }
        }
    }
}
