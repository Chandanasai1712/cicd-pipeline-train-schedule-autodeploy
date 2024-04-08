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
                sh './gradlew build --scan --no-daemon'
                archiveArtifacts artifacts:'dist/trainSchedule.zip'
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
        stage('kubernetesDeploy') {
            /* when {
                branch 'master'
            } */
            environment { 
                CANARY_REPLICAS = 1
            }
            steps {
                echo 'started'
                kubernetesDeploy(
                    kubeconfigId: 'kubeconfig',
                    configs: 'train-schedule-kube-canary.yml',
                    enableConfigSubstitution: true
                )
                echo 'completed'
                sh 'scm kubectl apply -f train-schedule-kube-canary.yml'
            }
        }
        // stage('DeployToProduction') {
        //     /* when {
        //         branch 'master'
        //     } */
        //     environment { 
        //         CANARY_REPLICAS = 0
        //     }
        //     steps {
        //         input 'Deploy to Production?'
        //         milestone(1)
        //         kubernetesDeploy(
        //             kubeconfigId: 'kubeconfig',
        //             configs: 'train-schedule-kube-canary.yml',
        //             enableConfigSubstitution: true
        //         )
        //         kubernetesDeploy(
        //             kubeconfigId: 'kubeconfig',
        //             configs: 'train-schedule-kube.yml',
        //             enableConfigSubstitution: true
        //         )
        //         echo 'completed 2'
        //     }
        // } 
        // stage('Deploy to Kubernetes') {
        //     steps {
        //         script {
        //             sh """
        //                 kubectl --kubeconfig=\$KUBECONFIG apply -f train-schedule-kube-canary.yml
        //                 kubectl --kubeconfig=\$KUBECONFIG apply -f train-schedule-kube.yml
        //             """
        //         }
        //     }
        // }
    //   stage('Deploy Canary') {
    //         steps {
    //             script {
    //                 // Define the Canary replicas count
    //                 def CANARY_REPLICAS = 1
                    
    //                 // Apply the Kubernetes resources
    //                  sh   'kubectl apply -f train-schedule-kube-canary.yml --validate=false'
    //             }
    //         }
    //     }
        
        // stage('Deploy Stable') {
        //     steps {
        //         script {
        //             // Define the Stable replicas count
        //             def STABLE_REPLICAS = 2
                    
        //             // Apply the Kubernetes resources
        //             sh """
        //                 ls -l
        //                 kubectl apply -f train-schedule-stable.yml
        //                 kubectl scale deployment/train-schedule-deployment --replicas=${STABLE_REPLICAS}
        //             """
        //         }
        //     }
        // }
     }
}
