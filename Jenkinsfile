pipeline {
    agent any

    environment {
        OCP_API_URL = 'https://C2C4E47CE918CDF651DAECF73D82428D.gr7.ap-south-1.eks.amazonaws.com'
        kubeconfig= credentials('eksconfig')
    }

    parameters {
        extendedChoice(
            name: 'SERVICES_TO_SCALE',
            type: 'PT_CHECKBOX',
            description: 'Select the services to scale',
            multiSelectDelimiter: ',',
            value: 'my-dep,nginx-deployment'
        )
        choice(name: 'NAMESPACE', choices: 'manjari', description: 'Namespace of the services')
        choice(name: 'REPLICAS', choices: ['0', '1'], description: 'Number of replicas')
    }

    stages {
        stage('Scale Deployment') {
            steps {
                script {
                    def services = params.SERVICES_TO_SCALE.split(',')
                    services.each { service ->
                        try {
                            sh """
                                kubectl scale deployment ${service} --replicas=${params.REPLICAS} --namespace=${params.NAMESPACE}
                            """
                            echo "Scaled ${service} to ${params.REPLICAS} replicas"
                        } catch (Exception e) {
                            echo "Error scaling ${service}: ${e.message}"
                            currentBuild.result = 'FAILURE'
                        }
                    }
                }
            }
        }
    }
}
