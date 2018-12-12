node("${SLAVE}") {
    stage('Preparation') {
        checkout scm
    }

    stage('Building code') {
        def mvnHome = tool name: '3.6.0', type: 'maven'
        sh '${mvnHome} -f helloworld-ws/pom.xml package'
    }

    stage('Testing') {
    }

    stage('Triggering job and fetching artefact after finishing') {
    }

    stage('Packaging and Publishing results') {
    }

    stage('Asking for manual approval') {
    }

    stage('Deployment') {
    }
}
