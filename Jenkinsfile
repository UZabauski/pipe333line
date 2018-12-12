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
        sh 'tar -xvf uzabauski_dsl_script.tar.gz'
        sh 'cp -f helloworld-ws/target/helloworld-ws.war .'
        sh 'tar -cvf pipeline-uzabauski-${BUILD_NUMBER}.tar.gz helloworld-ws.war jobs.groovy Jenkinsfile'
        archiveArtifacts 'pipeline-uzabauski-${BUILD_NUMBER}.tar.gz'
                


sh 'curl -v -u admin:admin123 -X POST \'http://10.6.204.41:8081/nexus/service/rest/v1/components?repository=maven-releases\' -F maven2.groupId=pipeline -F maven2.artifactId=app -F maven2.version=${BUILD_NUMBER} -F maven2.asset1=@app-${BUILD_NUMBER}.tar.gz -F maven2.asset1.extension=tar.gz' 
    }

    stage('Asking for manual approval') {
    }

    stage('Deployment') {
    }
}
