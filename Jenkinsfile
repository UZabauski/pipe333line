node("${SLAVE}") {
    def mvnHome = tool name: 'mavenLocal', type: 'maven'

    stage('Preparation') {
        checkout scm
    }

    stage('Building code') {
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
        build job: 'MNTLAB-uzabauski-child1-build-job', parameters: [string(name: 'BRANCH_NAME', value: 'uzabauski')], quietPeriod: 0
            step ([$class: 'CopyArtifact', projectName: 'MNTLAB-uzabauski-child1-build-job', filter: 'uzabauski_dsl_script.tar.gz']);
    }

    stage('Packaging and Publishing results') {
    }

    stage('Asking for manual approval') {
    }

    stage('Deployment') {
    }
}
