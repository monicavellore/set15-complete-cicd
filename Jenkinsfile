
pipeline {
    agent any

    environment {
        DOCKER = 'C:\\Users\\admin\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\docker.exe'
        KUBECTL = 'C:\\Users\\admin\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\kubectl.exe'
        KUBECONFIG = 'C:\\Users\\admin\\.kube\\config'
        IMAGE = 'yazhini01/set15-web:1.0'
    }

    stages {
        stage('Checkout Source') {
            steps {
                checkout scm
                bat 'dir'
                bat 'dir app'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat '"%DOCKER%" build -t %IMAGE% .'
            }
        }

        stage('Test Application') {
            steps {
                bat '"%DOCKER%" run --rm %IMAGE% nginx -t'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-cred',
                    usernameVariable: 'DOCKERHUB_USER',
                    passwordVariable: 'DOCKERHUB_TOKEN'
                )]) {
                    powershell '''
                        $env:DOCKERHUB_TOKEN | & $env:DOCKER login -u $env:DOCKERHUB_USER --password-stdin
                        if ($LASTEXITCODE -ne 0) { exit $LASTEXITCODE }

                        & $env:DOCKER push $env:IMAGE
                        exit $LASTEXITCODE
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                bat '"%KUBECTL%" --kubeconfig="%KUBECONFIG%" apply -f "%WORKSPACE%\\k8s\\deployment.yaml"'
                bat '"%KUBECTL%" --kubeconfig="%KUBECONFIG%" rollout status deployment/set15-web -n lab-sets --timeout=180s'
            }
        }

        stage('Verify Deployment') {
            steps {
                bat '"%KUBECTL%" --kubeconfig="%KUBECONFIG%" get deployments -n lab-sets'
                bat '"%KUBECTL%" --kubeconfig="%KUBECONFIG%" get pods -n lab-sets'
                bat '"%KUBECTL%" --kubeconfig="%KUBECONFIG%" get services -n lab-sets'
            }
        }
    }
}
