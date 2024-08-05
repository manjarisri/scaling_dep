pipeline {
    agent any

    environment {
        OCP_API_URL = 'http://127.0.0.1:35587'  // Replace with your OpenShift API URL
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
        choice(name: 'REPLICAS', choices: ['0','1'], description: 'Replica of the services')
    }

    stages {
        stage('Scale Deployment') {
            steps {
                script {
                    try {
                        def scaleResponse = httpRequest(
                            httpMode: 'PUT',
                            url: "${env.OCP_API_URL}/apis/apps/v1/namespaces/${params.NAMESPACE}/deployments/${params.SERVICE}/scale",
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
                        echo "Response Code: ${scaleResponse.status}"
                        echo "Response Body: ${scaleResponse.content}"
                    } catch (Exception e) {
                        echo "Error: ${e.message}"
                        currentBuild.result = 'FAILURE' // Mark build as failed
                    }
                }
            }
        }
}
