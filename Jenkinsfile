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
        HELM_REPO_URL = "gs://helmrepo/myservice"
        DEPLOYMENT_NAME = "my-deployment"
        HELM_RELEASE_NAME = "Mysrevice"
        DOCKER_IMAGE_TAG = 76

    }
       
    stages{
        
        stage("Aauthenticate with GCP"){
            steps{
                script{
                    sh 'gcloud config set project ${GCR_PROJECT_ID}'
                    sh 'gcloud auth activate-service-account --key-file=/opt/devops.json'
                    sh 'gcloud container clusters get-credentials ${GKE_CLUSTER} --zone ${GKE_ZONE} --project ${GCR_PROJECT_ID}'
                }
            }
            
        }
        // stage("Creating Helm charts"){
        //     steps{
        //         script{
        //             sh 'helm create ${HELM_CHART_DIR}'

        //         }
        //     }
        // }
        stage("Create helm package"){
            steps{
                script{
                    sh '''
                    
                    cd ${HELM_CHART_DIR}
                    rm -rf *.tgz
                    sed -i "s/TAG/$DOCKER_IMAGE_TAG/g" values.yaml
                    helm package .
                    mv *.tgz ${DOCKER_IMAGE_TAG}.tgz
                    '''
                    
                }
            }
        }
        stage('Installing the helm chart'){
            steps{
                script{
                    sh 'cd ${HELM_CHART_DIR}'
                    // sh 'helm repo add ${HELM_REPO_NAME} ${HELM_REPO_URL}'
                    sh 'helm install --version ${DOCKER_IMAGE_TAG} helm-${HELM_CHART_DIR} -f  values.yaml *.tgz'
                }
            }
        }
        stage("push helm charts"){
            steps{
                script{
                    // sh 'helm repo add ${HELM_REPO_NAME} ${HELM_REPO_URL}'
                    sh 'gsutil cp ${DOCKER_IAMGE_TAG}.tgz ${HELM_REPO_URL}'
                }
            }
        }
        
    }
}
