#!/usr/bin/groovy
@Library('github.com/stakater/fabric8-pipeline-library@add-funcs')
String chartPackageName = ""
String chartName = "helloworld"

clientsNode(clientsImage: 'stakater/kops-ansible:helm-bundle') {
    container(name: 'clients') {
        stage('Checkout') {
            checkout scm
        }
        
        stage('Init Helm') {
            def helm = new io.stakater.charts.Helm(this, WORKSPACE, chartName);
            sh "helm init --client-only"

            def result = io.stakater.Common.shOutput this, """
                echo "Testing"
            """
            println result
        }

        stage('Prepare Chart') {
            chartPackageName = prepareChart(chartName)
        }

        stage('Upload Chart') {
            uploadChart(chartName, chartPackageName)
        }
    }
}

def prepareChart(String chartName) {
    result = shOutput """
                cd ${WORKSPACE}/${chartName}
                helm lint
                helm package .
            """

    return result.substring(result.lastIndexOf('/') + 1, result.length())
}

def uploadChart(String chartName, String fileName) {
    sh """
        cd ${WORKSPACE}/${chartName}
        curl -L --data-binary \"@${fileName}\" http://chartmuseum/api/charts
    """
}

def shOutput(String command) {
    return sh(
        script: """
            ${command}
        """,
        returnStdout: true).trim()
}
