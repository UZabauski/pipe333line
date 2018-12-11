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
	sh 'tar -czf hello_war-${BUILD_NUMBER}.tar.gz jobs.groovy Jenkinsfile helloworld-ws/target/helloworld-ws.war'
        archiveArtifacts 'hello_war-${BUILD_NUMBER}.tar.gz'
        nexusPublisher nexusInstanceId: 'nexus3', 
          nexusRepositoryId: 'maven-releases', 
          packages: [[$class: 'MavenPackages', 
            mavenAssetList: [[classifier: '', extension: '', 
            filePath: 'hello_war-${BUILD_NUMBER}.tar.gz']], 
          mavenCoordinate: [artifactId: 'Hello', 
            groupId: 'pipeline', 
            packaging: 'tar', 
            version: '1.$BUILD_NUMBER']]]
		}
	}
