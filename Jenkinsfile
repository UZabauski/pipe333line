node("${SLAVE}") {
    stage('Preparation') {
        checkout scm
    }

    stage('Building code') {
        sh 'mvn -f helloworld-ws/pom.xml package'
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
