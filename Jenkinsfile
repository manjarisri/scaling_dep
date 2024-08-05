pipeline {
    agent any

    environment {
        OCP_API_URL = 'https://C2C4E47CE918CDF651DAECF73D82428D.gr7.ap-south-1.eks.amazonaws.com'  // Replace with your OpenShift API URL
        OCP_TOKEN = credentials('eks-token')  // Replace with your Jenkins secret ID
    }

    parameters {
        // Multi-select checkbox parameter for services
        extendedChoice(
            name: 'SERVICES_TO_SCALE',
            type: 'PT_CHECKBOX',
            description: 'Select the services to scale',
            multiSelectDelimiter: ',',
            value: 'my-dep,nginx-deployment'
        )
        choice(name: 'NAMESPACE', choices: 'manjari', description: 'Namespace of the services')
        choice(name: 'REPLICAS', choices: ['0','1'], description: 'Number of replicas')
    }

    stages {
        stage('Scale Deployments') {
            steps {
                script {
                    def services = params.SERVICES_TO_SCALE.split(',')  // Split selected services
                    services.each { service ->
                        try {
                            def scaleResponse = httpRequest(
                                httpMode: 'POST',
                                url: "${env.OCP_API_URL}/apis/apps/v1/namespaces/${params.NAMESPACE}/deployments/${service}/scale",
                                customHeaders: [
                                    [name: 'Authorization', value: "Bearer ${env.OCP_TOKEN}"],
                                    [name: 'Content-Type', value: 'application/json']
                                ],
                                requestBody: """{
                                    "spec": {
                                        "replicas": ${params.REPLICAS}
                                    }
                                }""",
                                ignoreSslErrors: true // Ignoring SSL errors
                            )
                            echo "Scaled service ${service}: Response Code: ${scaleResponse.status}"
                            echo "Response Body: ${scaleResponse.content}"
                        } catch (Exception e) {
                            echo "Error scaling service ${service}: ${e.message}"
                            currentBuild.result = 'FAILURE' // Mark build as failed
                        }
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up after build'
            // Add any cleanup steps here if needed
        }
        success {
            echo 'Services scaled successfully'
        }
        failure {
            echo 'Scaling services failed'
        }
    }
}
