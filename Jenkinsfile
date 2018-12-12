node('EPBYMINW2466') {
    stage("Preparation (Checking out)") {
    	checkout([$class: 'GitSCM', 
        branches: [[name: '*/askorkin']], 
        userRemoteConfigs: [[url: 'https://github.com/MNT-Lab/pipe333line.git']]])
    }
    stage ("Building code") {
        sh '/home/student/Programs/apache-maven-3.6.0/bin/mvn compile -f helloworld-ws/pom.xml'
        sh '/home/student/Programs/apache-maven-3.6.0/bin/mvn package -f helloworld-ws/pom.xml'
    }
    stage ("Testing") {
	parallel(
            a: {sh '/home/student/Programs/apache-maven-3.6.0/bin/mvn pre-integration-test -f helloworld-ws/pom.xml'},
	    b: {sh '/home/student/Programs/apache-maven-3.6.0/bin/mvn integration-test -f helloworld-ws/pom.xml'},
	    c: {sh '/home/student/Programs/apache-maven-3.6.0/bin/mvn post-integration-test -f helloworld-ws/pom.xml'}
	)
    }
    stage ("Triggering job and fetching artefact after finishing") {
        build job: 'MNTLAB-askorkin-child1-build-job',
        parameters: [gitParameter(name: 'BRANCH_NAME', value: 'askorkin')]
	    copyArtifacts filter: 'jobs.groovy', projectName: 'MNTLAB-askorkin-child1-build-job'
    }
    stage ("Packaging and Publishing results") {
	    sh 'tar -czf app-${BUILD_NUMBER}.tar.gz jobs.groovy Jenkinsfile helloworld-ws/target/helloworld-ws.war'
            archiveArtifacts 'app-${BUILD_NUMBER}.tar.gz'
	    sh 'curl -v -u admin:admin123 -X POST \'http://10.6.205.35:8081/service/rest/v1/components?repository=maven-releases\' -F maven2.groupId=pipeline -F maven2.artifactId=app -F maven2.version=1.${BUILD_NUMBER} -F maven2.asset1=@app-${BUILD_NUMBER}.tar.gz -F maven2.asset1.extension=tar.gz'
    }
}
