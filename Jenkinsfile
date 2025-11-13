pipeline {
agent any

environment {
PROJECT_ID = 'applied-pager-476808-j5'
CLUSTER_NAME = 'gke-cluster'
CLUSTER_ZONE = 'us-central1-a'
REGION = 'us-central1'
GCP_CREDENTIALS = 'serviceaccountkey'
}

 stages {
       stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/TCRDINSEH/microservices-demo.git',
                        
            }
        }

    stage('Authenticate to GCP') {
      steps {
        withCredentials([file(credentialsId: "${GCP_CREDENTIALS}", variable: 'GCP_KEYFILE')]) {
          sh '''
            echo "🔐 Authenticating to GCP..."
            gcloud auth activate-service-account --key-file=$GCP_KEYFILE
            gcloud config set project ${PROJECT_ID}
          '''
        }
      }
    }
 }

       stage('Deploy to GKE') {
           steps {
               withCredentials([file(credentialsId: "${GCP_CREDENTIALS}", variable: 'GCLOUD_KEY')]) {
                   sh '''
                        echo "Deploying to GKE..."
                        echo "Deploying to GKE..." */
                '''
               }
           }
       }
       
        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([file(credentialsId: "${GCP_CREDENTIALS}", variable: 'GCLOUD_KEY')]) {
                echo "☸️ Deploying microservices using Kubernetes manifests..."
                sh '''
                    # Apply all manifests in the kubernetes-manifests directory
                    kubectl apply -f kubernetes-manifests/
                    sleep 120
                    kubectl get pods

                '''
                
            }
        }
    }

    post {
        success {
            echo "✅ All microservices deployed successfully!"
        }
        failure {
            echo "❌ Deployment failed. Please check logs above."
        }
    }
}