node("${SLAVE}") {
    stage("Preparation") {
        checkout([$class: 'GitSCM', 
        branches: [[name: '*/rbachakrou']], 
        doGenerateSubmoduleConfigurations: false, 
        extensions: [], 
        submoduleCfg: [], 
        userRemoteConfigs: [[url: 'https://github.com/MNT-Lab/pipe333line.git']]])
    }
    stage ("Building code") {
        sh '/home/student/Documents/apache-maven-3.6.0/bin/mvn compile -f helloworld-ws/pom.xml'
	sh '/home/student/Documents/apache-maven-3.6.0/bin/mvn package -f helloworld-ws/pom.xml'
        
    }
    stage ("Testing") {
	parallel(
            a: {sh '/home/student/Documents/apache-maven-3.6.0/bin/mvn pre-integration-test -f helloworld-ws/pom.xml'},
	    b: {sh '/home/student/Documents/apache-maven-3.6.0/bin/mvn integration-test -f helloworld-ws/pom.xml'},
	    c: {sh '/home/student/Documents/apache-maven-3.6.0/bin/mvn post-integration-test -f helloworld-ws/pom.xml'}
	)
    }
    stage ("Triggering job and fetching artefact after finishing") {
        build job: 'MNTLAB-rbachakrou-child1-build-job', parameters: [gitParameter(name: 'BRANCH_NAME', value: 'rbachakrou')]
	copyArtifacts filter: 'jobs.groovy', projectName: 'MNTLAB-rbachakrou-child1-build-job'
    }
stage ("Push to nexus") {
	sh 'tar -czf hellowar-${BUILD_NUMBER}.tar.gz jobs.groovy Jenkinsfile helloworld-ws/target/helloworld-ws.war'
        archiveArtifacts 'hellowar-${BUILD_NUMBER}.tar.gz'
	sh 'curl -v -u admin:admin123 -X POST \'http://10.6.204.104:8081/service/rest/v1/components?repository=Maven_release\' -F maven2.groupId=pipeline -F maven2.artifactId=hellowar -F maven2.version=1.${BUILD_NUMBER} -F maven2.asset1=@hellowar-${BUILD_NUMBER}.tar.gz -F maven2.asset1.extension=tar.gz'
		}
	}
 stage ("Deployment") {
	sh 'docker run -tid -p 80${BUILD_NUMBER}:8080 -name=tomcat_${BUILD_NUMBER} tomcat'
	sh 'docker exec tomcat_${BUILD_NUMBER} bash -c \'curl -u admin:admin123 -o hellowar-${BUILD_NUMBER}.tar.gz  http://10.6.204.104:8091/repository/jenkins-data/pipeline/hellowar/1.${BUILD_NUMBER}/hellowar-${BUILD_NUMBER}.tar.gz && tar xfv hellowar-${BUILD_NUMBER}.tar.gz helloworld-ws/target/helloworld-ws.war --strip-components 2 && mv helloworld-ws.war webapps\''
   }
