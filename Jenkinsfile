pipeline {
    agent any

    environment {
        PROJECT_ID      = 'applied-pager-476808-j5'
        REGION          = 'us-central1'
        CLUSTER_NAME    = 'gke-cluster'
        ZONE            = 'us-central1-a'
        REPO            = 'microservices-demo'
        GCP_CREDENTIALS = 'serviceaccountkey'
    }

    stages {

        stage('Checkout Source Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/TCRDINSEH/microservices-demo.git'
            }
        }

        stage('Authenticate to GCP') {
            steps {
                withCredentials([file(credentialsId: "${GCP_CREDENTIALS}", variable: 'GCLOUD_KEY')]) {
                    sh '''
                        echo "🔐 Authenticating to Google Cloud..."
                        gcloud auth activate-service-account --key-file=$GCLOUD_KEY
                        gcloud config set project ${PROJECT_ID}
                        gcloud container clusters get-credentials ${CLUSTER_NAME} --zone ${ZONE}
                        gcloud auth configure-docker ${REGION}-docker.pkg.dev --quiet
                    '''
                }
            }
        }

     stage('Build & Push Docker Images') {
    steps {
        sh '''
        set -e
        COMMIT_ID=$(git rev-parse --short HEAD)
        echo "Using image tag: $COMMIT_ID"
        BASE_DIR="src"

        for SERVICE in adservice checkoutservice currencyservice emailservice frontend loadgenerator paymentservice productcatalogservice recommendationservice shippingservice
        do
            echo "🚀 Building $SERVICE"
            docker build -t ${REGION}-docker.pkg.dev/${PROJECT_ID}/${REPO}/${SERVICE}:$COMMIT_ID $BASE_DIR/$SERVICE
            docker push ${REGION}-docker.pkg.dev/${PROJECT_ID}/${REPO}/${SERVICE}:$COMMIT_ID
        done
        '''
    }
}


        stage('Deploy to GKE') {
    steps {
        sh '''
            echo "🚀 Deploying to Kubernetes..."
            kubectl apply -f kubernetes-manifests/

            echo "⏳ Waiting for all deployments to become ready..."
            for deploy in $(kubectl get deployments -o name); do
                kubectl rollout status $deploy
            done
        '''
    }
}

    }

    post {
        success {
            echo "✅ CI/CD Pipeline completed successfully!"
        }
        failure {
            echo "❌ Pipeline failed. Check logs above."
        }
    }
}