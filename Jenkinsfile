pipeline{
    
    agent any
    
    environment {
        AZURE_TENANT_ID="2f0b93a1-027d-4ff3-8c5e-b0eade655f77"
        AZURE_SUBSCRIPTION_ID="3f90d050-8436-406b-9fab-303ffaef8a42"
        ACR_NAME='learningdevops'
        RESOURCE_GROUP='rg-dnk'
        IMAGE_NAME='javaimagednk'
        IMAGE_TAG='1'
    }
    
    stages{
        stage('Check out code'){
            steps{
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/DODDAKA/learnjenkins']])
            }
        }
        stage('Build Maven'){
            
            steps{
                sh 'mvn clean install'
                
            }
        }
        stage('Build docker image'){
            steps{
                script{
                    sh 'docker build -t ${IMAGE_NAME} .'
                }
            }
        }
        
        stage('Push image to ACR') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'myAzureCredential', passwordVariable: 'AZURE_CLIENT_SECRET', usernameVariable: 'AZURE_CLIENT_ID')]) {
                            sh 'az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID'
                            sh 'az account set -s $AZURE_SUBSCRIPTION_ID'
                            sh 'az acr login --name $ACR_NAME --resource-group $RESOURCE_GROUP'
                            sh 'docker tag ${IMAGE_NAME} ${ACR_NAME}.azurecr.io/${IMAGE_NAME}:${IMAGE_TAG}'
                            sh 'docker push ${ACR_NAME}.azurecr.io/${IMAGE_NAME}:${IMAGE_TAG}'
                }
            }
        }
            
            
    }

}