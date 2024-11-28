pipeline {
    agent any

    environment {
        AWS_REGION = 'us-west-2'
        // Add other necessary environment variables here
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the code from the repository
                checkout scm
            }
        }

        stage('Build') {
            steps {
                script {
                    // Build the application
                    sh 'make build'  // Modify this according to your build process
                }
            }
        }

        stage('Unit Test') {
            steps {
                script {
                    // Run unit tests
                    sh 'make test'  // Modify this according to your test process
                }
            }
        }

        stage('Static Code Analysis') {
            steps {
                script {
                    // Run static code analysis
                    sh 'make lint'  // Modify this according to your static analysis tool
                }
            }
        }

        stage('Security Scan') {
            steps {
                script {
                    // Run security scans
                    sh 'make security-scan'  // Modify this according to your security scanning tool
                }
            }
        }

        stage('Terraform Init') {
            steps {
                script {
                    // Initialize Terraform
                    sh 'terraform init'
                }
            }
        }

        stage('Terraform Plan') {
            steps {
                script {
                    // Plan Terraform changes based on the branch
                    if (env.BRANCH_NAME == 'development') {
                        sh 'terraform plan -var-file=dev.tfvars'
                    } else if (env.BRANCH_NAME == 'staging') {
                        sh 'terraform plan -var-file=staging.tfvars'
                    } else if (env.BRANCH_NAME == 'production') {
                        sh 'terraform plan -var-file=prod.tfvars'
                    }
                }
            }
        }

        stage('Terraform Apply') {
            steps {
                script {
                    // Apply Terraform changes based on the branch
                    if (env.BRANCH_NAME == 'development') {
                        sh 'terraform apply -var-file=dev.tfvars -auto-approve'
                    } else if (env.BRANCH_NAME == 'staging') {
                        sh 'terraform apply -var-file=staging.tfvars -auto-approve'
                    } else if (env.BRANCH_NAME == 'production') {
                        sh 'terraform apply -var-file=prod.tfvars -auto-approve'
                    }
                }
            }
        }
        
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Deploy application to Kubernetes cluster
                    deployToK8s(env.BRANCH_NAME)
                }
            }
        }
    }

    post {
        always {
            cleanWs()  // Clean workspace after each build
        }
    }
}

def deployToK8s(environment) {
    withCredentials([string(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
        sh """
        kubectl apply -f k8s/${environment}/deployment.yaml
        kubectl apply -f k8s/${environment}/service.yaml
        """
    }
}
