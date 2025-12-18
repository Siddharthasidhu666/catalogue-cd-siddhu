pipeline {
    agent {
        label 'AGENT-1'
    }
    environment {
         appVersion = ''
         REGION = "us-east-1"
         ACC_ID = "590183868932"
         PROJECT = "roboshop"
         COMPONENT = "catalogue"


    }
    parameters {
        string(name: 'appVersion', description:'Image version of the application')
        choice(name: 'deploy_to',, choices: ['dev', 'prod', 'qa'], description: 'choose the environment')
        
    }
    options {
        // Timeout counter starts AFTER agent is allocated
        timeout(time: 15, unit: 'MINUTES')
        disableConcurrentBuilds()
    }
    stages {
        stage('deploy app to eks') {
            steps {
                script {
                    withAWS(credentials: 'aws-cred', region: 'us-east-1') {
                        sh """
                            aws eks update-kubeconfig --region $REGION --name "$PROJECT-${params.deploy_to}"
                            kubectl get nodes
                            kubectl apply -f 01-namespace.yaml
                            sed -i "s/IMAGE_VERSION/${params.appVersion}/g" values-${params.deploy_to}.yaml
                            helm upgrade --install $COMPONENT -f values-${params.deploy_to}.yaml -n $PROJECT .

                        """
                    }

                }
            }
        }
    }
    post { 
        always { 
            echo 'I will always say Hello again!'
            deleteDir()
        }
        success { 
            echo 'Hello Success'
        }
        failure { 
            echo 'Hello Failure'
        }
        
    }
}
