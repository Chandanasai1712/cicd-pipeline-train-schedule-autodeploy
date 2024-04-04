pipeline {
    agent any
    environment {
        //be sure to replace "bhavukm" with your own Docker Hub username
        DOCKER_IMAGE_NAME = "chandana1712/train-schedule"
    }
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts:'dist/trainchandana.zip'
            }
        }
        stage('Build Docker Image') {
           /*  when {
                branch 'master'
            } */
            steps {
                script {
                    app = docker.build(DOCKER_IMAGE_NAME)
                    app.inside {
                        sh 'echo Hello, World!'
                    }
                }
            }
        }
        stage('Push Docker Image') {
            /* when {
                branch 'master'
            } */
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
    // 0    stage('CanaryDeploy') {
    //         /* when {
    //             branch 'master'
    //         } */
    //         environment { 
    //             CANARY_REPLICAS = 1
    //         }
    //         steps {
    //             // echo 'started'
    //             kubernetesDeploy(
    //                 kubeconfigId: 'kubeconfig',
    //                 configs: 'train-schedule-kube-canary.yml',
    //                 enableConfigSubstitution: true
    //             )
    //             // echo 'completed'
    //             // sh 'kubectl apply -f train-schedule-kube-canary.yml'
    //         }
    //     }
    //     stage('DeployToProduction') {
    //         /* when {
    //             branch 'master'
    //         } */
    //         environment { 
    //             CANARY_REPLICAS = 0
    //         }
    //         steps {
    //             input 'Deploy to Production?'
    //             milestone(1)
    //             kubernetesDeploy(
    //                 kubeconfigId: 'kubeconfig',
    //                 configs: 'train-schedule-kube-canary.yml',
    //                 enableConfigSubstitution: true
    //             )
    //             kubernetesDeploy(
    //                 kubeconfigId: 'kubeconfig',
    //                 configs: 'train-schedule-kube.yml',
    //                 enableConfigSubstitution: true
    //             )
    //             echo 'completed 2'
    //         }
    //     }
       stage('Deploy') {
            environment {
                // Define any environment variables needed for deployment
                KUBE_NAMESPACE = 'default'
                KUBE_SERVER = 'https://31E40A1B501B7942D605D20A0EBF3B84.gr7.us-east-1.eks.amazonaws.com'
                KUBE_CREDENTIALS = credentials('kube')
            }
            steps {
                // Deploy to Kubernetes
                script {
                    kubernetesDeploy(
                        kubeconfigId: 'kubeconfig', // Optional if Jenkins can access kubeconfig
                        configs: 'var/lib/jenkins/workspace/train/*.yaml', // Path to Kubernetes YAML manifests
                        kubeServerUrl: "${KUBE_SERVER}",
                        kubeNamespace: "${default}",
                        kubeCredentialId: "${kube}"
                    )
                }
            }
        }
    }
}
