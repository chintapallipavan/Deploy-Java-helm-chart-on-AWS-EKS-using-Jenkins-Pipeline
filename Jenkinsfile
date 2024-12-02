pipeline {
    agent any

    stages {
        stage("Clone code from GitHub") {
            steps {
                script {
                    checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'GITHUB_CREDENTIALS', url: 'https://github.com/devopshint/Deploy-Java-helm-chart-on-AWS-EKS-using-Jenkins-Pipeline/']])
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    // Run Maven to build the project and generate the WAR file
                    sh 'mvn clean package'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image
                    sh 'docker build -t devopshint/java-1.0 .'
                }
            }
        }

        stage('Deploy Docker Image to DockerHub') {
            steps {
                script {
                    // Use the credentials with ID 'devopshint' for Docker login
                    withCredentials([usernamePassword(credentialsId: 'devopshint', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh 'docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD'
                    }
                    // Correct docker tag command
                    sh 'docker tag java-1.0 $DOCKER_USERNAME/java-1.0:latest'
                    sh 'docker push $DOCKER_USERNAME/java-1.0:latest'
                }
            }
        }

        stage('Deploying Node App Helm Chart on EKS') {
            steps {
                script {
                    // Update Kubernetes config for EKS
                    sh 'aws eks update-kubeconfig --name my-eks-cluster --region us-west-2'
                    // Ensure kubectl can connect to the cluster
                    sh 'kubectl get ns'
                    // Install the Helm chart
                    sh 'helm install java ./java-chart'
                }
            }
        }
    }
}

