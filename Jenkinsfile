pipeline {
    agent any

    environment {
        OCP_API_URL = 'https://192.168.49.2:8443'  // Replace with your OpenShift API URL
        OCP_TOKEN = credentials('minikube-token')  // Replace with your Jenkins secret ID
    }

    parameters {
        // Add a multi-select checkbox parameter for services
        extendedChoice(
            name: 'SERVICES_TO_SCALE',
            type: 'PT_CHECKBOX',
            description: 'Select the services to scale',
            multiSelectDelimiter: ',',
            value: 'my-dep,nginx-deployment'
        )
        choice(name: 'NAMESPACE', choices: 'manjari', description: 'Namespace of the services')
        choice(name: 'REPLICAS', choices: '['0','1'], description: 'Replica of the services')
    }

    stages {
        stage('Scale Deployments') {
            steps {
                script {
                    // Split the comma-separated services into a list
                    def services = params.SERVICES_TO_SCALE.split(',')

                    // Iterate over each service and scale it
                    services.each { service ->
                        echo "Scaling service: ${service}"

                        def scaleResponse = httpRequest(
                            httpMode: 'PUT',
                            url: "${env.OCP_API_URL}/apis/apps/v1/namespaces/${params.NAMESPACE}/deployments/${service}/scale",
                            customHeaders: [
                                [name: 'Authorization', value: "Bearer ${env.OCP_TOKEN}"],
                                [name: 'Content-Type', value: 'application/json']
                            ],
                            requestBody: """{
                                "spec": {
                                    "replicas": ${params.REPLICAS}
                                }
                            }"""
                        )
`
                        if (scaleResponse.status != 200) {
                            error "Scaling failed for service ${service}: ${scaleResponse.content}"
                        }

                        echo "Scaling successful for service: ${service}"
                    }
                }
            }
        }
    }
}
