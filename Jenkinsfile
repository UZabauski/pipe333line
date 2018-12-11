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
    stage ("Packaging and Publishing results") {
        sh 'tar -czf pipeline-aisachanka-${BUILD_NUMBER}.tar.gz jobs.groovy Jenkinsfile helloworld-ws/target/helloworld-ws.war'
        archiveArtifacts 'pipeline-aisachanka-${BUILD_NUMBER}.tar.gz'
        nexusPublisher nexusInstanceId: 'nexus3', 
          nexusRepositoryId: 'maven-releases', 
          packages: [[$class: 'MavenPackage', 
            mavenAssetList: [[classifier: '', extension: '', 
            filePath: 'pipeline-aisachanka-${BUILD_NUMBER}.tar.gz']], 
          mavenCoordinate: [artifactId: 'aisachanka', 
            groupId: 'pipeline', 
            packaging: 'tar', 
            version: '1.$BUILD_NUMBER']]]
    }
}
