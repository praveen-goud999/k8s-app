pipeline {
    agent any

    environment {
        GOOGLE_APPLICATION_CREDENTIALS = credentials('gcp-service-account') // Ensure service account JSON is available
        GKE_CLUSTER_NAME = 'cluster-1' // Update with your GKE cluster name
        GKE_PROJECT_ID = 'kiran400' // Update with your GCP project ID
        GKE_ZONE = 'us-central1' // Update with your GKE cluster zone
        GIT_REPO_URL = 'https://github.com/praveen-goud999/k8s-app.git' // Update with your Git repository URL
        GIT_BRANCH = 'main' // Update with your branch name if needed (default is 'main')
    }

    stages {
        stage('Checkout Git Repository') {
            steps {
                script {
                    // Checkout the Git repository containing your Kubernetes manifests
                    git url: "${GIT_REPO_URL}", branch: "${GIT_BRANCH}"
                }
            }
        }

    stage('Authenticate with GCP') {
    steps {
        withCredentials([file(credentialsId: 'gcp-service-account-key', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
            script {
                // Use shell-safe quoting to avoid secret exposure
                sh 'gcloud auth activate-service-account --key-file="$GOOGLE_APPLICATION_CREDENTIALS"'
                sh 'gcloud config set project "$GKE_PROJECT_ID"'
                sh 'gcloud config set compute/zone "$GKE_ZONE"'
            }
        }
    }
}



        stage('Configure kubectl') {
            steps {
                script {
                    // Get credentials for the GKE cluster
                    sh 'gcloud container clusters get-credentials ${GKE_CLUSTER_NAME}'
                }
            }
        }

        stage('Deploy to GKE') {
            steps {
                script {
                    // Apply the Kubernetes YAML files to deploy the app
                    sh 'kubectl apply -f aks-store-quickstart.yaml' // Specify the path to your YAML files after checkout
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                script {
                    // Verify the deployment status
                    sh 'kubectl get pods'
                    sh 'kubectl get svc'
                }
            }
        }
    }
}
