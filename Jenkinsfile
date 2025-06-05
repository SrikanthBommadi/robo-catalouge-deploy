pipeline{
    agent{
        label'jenkins-agent'
    }
    environment { 
        PROJECT = 'roboshop'
        COMPONENT = 'catalogue' 
        DEPLOY_TO = ""
        REGION = "us-east-1"
        appVersion = ''
        environment = ''
    }
     options {
        disableConcurrentBuilds()
        timeout(time: 30, unit: 'MINUTES')
    }
     parameters{
        string(name: 'version',  description: 'Enter the application version')
        choice(name: 'deploy_to', choices: ['dev', 'qa', 'prod'], description: 'Pick something')
    }
    stages{
        stage('Setup Environment'){
            steps{
                script{
                    appVersion = params.version
                    environment = params.deploy_to
                }
            }
        }
        stage('Deploy') {
            steps {
                script{
                    withAWS(region: 'us-east-1', credentials: 'aws') {
                        sh """
                            aws eks update-kubeconfig --region $REGION --name ${PROJECT}-developer
                            kubectl get nodes
                            cd helm
                            sed -i 's/IMAGE_VERSION/${params.version}/g' values-${environment}.yaml
                            helm upgrade --install $COMPONENT -n $PROJECT -f values-${environment}.yaml .
                        """
                    }
                }
            }
        }
        stage('Functional/apitesting'){
            when{
                expression{
                    params.deploy_to == 'dev'
                }
            }
            steps{
                script{
                    sh"""
                    echo selinum testing
                    """
                }
            }
        }
        stage('Integration testing') {
            when{
                expression {
                    params.deploy_to == 'qa'
                }
            }
            steps{
                script{
                    sh"""
                    echo it will be cucumber
                    """
                }
            }
        }
        
    }
    post { 
        always { 
            echo 'I will always say Hello again!'
            deleteDir()
        }
        failure { 
            echo 'I will run when pipeline is failed'
        }
        success { 
            echo 'I will run when pipeline is success'
        }
    }
}