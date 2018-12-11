node("${SLAVE}") {
    stage("Preparation") {
        checkout([$class: 'GitSCM', 
        branches: [[name: '*/aisachanka']], 
        doGenerateSubmoduleConfigurations: false, 
        extensions: [], 
        submoduleCfg: [], 
        userRemoteConfigs: [[url: 'https://github.com/MNT-Lab/pipe333line.git']]])
    }
    stage ("Building code") {
        sh 'mvn compile -f helloworld-ws/pom.xml'
	sh 'mvn package -f helloworld-ws/pom.xml'
    }
    stage ("Testing") {
	parallel(
            a: {sh 'mvn pre-integration-test -f helloworld-ws/pom.xml'},
	    b: {sh 'mvn integration-test -f helloworld-ws/pom.xml'},
	    c: {sh 'mvn post-integration-test -f helloworld-ws/pom.xml'}
	)
    }
    stage ("Triggering job and fetching artefact after finishing") {
        build job: 'MNTLAB-aisachanka-child1-build-job', parameters: [gitParameter(name: 'BRANCH_NAME', value: 'aisachanka')]
	copyArtifacts filter: 'jobs.groovy', projectName: 'MNTLAB-aisachanka-child1-build-job'
    }
    boolean packPassed = true
    stage ("Packaging and Publishing results") {
	try {
        sh 'tar -czf pipeline-aisachanka-${BUILD_NUMBER}.tar.gz jobs.groovy Jenkinsfile helloworld-ws/target/helloworld-ws.war'
        archiveArtifacts 'pipeline-aisachanka-${BUILD_NUMBER}.tar.gz'
	sh 'curl -v -u admin:admin123 -X POST \'http://10.6.205.104:8081/service/rest/v1/components?repository=jenkins-data\' -F maven2.groupId=pipeline -F maven2.artifactId=aisachanka -F maven2.version=${BUILD_NUMBER} -F maven2.asset1=@pipeline-aisachanka-${BUILD_NUMBER}.tar.gz -F maven2.asset1.extension=tar.gz'
	} catch (Exception e) {
        packPassed = false 
          }    
    }
    stage ("Asking for manual approval") {
        if(packPassed){
            input "Do you approve this?"
        }
    }
   stage ("Deployment") {
	sh 'docker exec -it my-tomcat bash -c \'curl -u admin:admin123 -o aisachanka-${BUILD_NUMBER}.tar.gz  http://10.6.205.104:8081/repository/jenkins-data/pipeline/aisachanka/${BUILD_NUMBER}/aisachanka-${BUILD_NUMBER}.tar.gz && tar xfv aisachanka-${BUILD_NUMBER}.tar.gz helloworld-ws/target/helloworld-ws.war --strip-components 2 && mv helloworld-ws.war webapps\''   
}
}
