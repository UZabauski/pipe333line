node("${SLAVE}") {
    stage('Preparation') {
        checkout scm
    }

    stage('Building code') {
        def mvnHome = tool name: 'mavenLocal', type: 'maven'
        sh "${mvnHome}/bin/mvn package -f helloworld-ws/pom.xml"
    }

    stage('Testing') {
        parallel (
        preTest: {sh "${mvnHome}/bin/mvn pre-integration-test -f helloworld-ws/pom.xml"},
        test: {sh "${mvnHome}/bin/mvn integration-test -f helloworld-ws/pom.xml"},
        postTest: {sh "${mvnHome}/bin/mvn post-integration-test -f helloworld-ws/pom.xml"}
        )
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
