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
        sh 'mvn clean install -f helloworld-ws/pom.xml'
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
        build job: 'MNTLAB-rbachakrou-child1-build-job', parameters: [gitParameter(name: 'BRANCH_NAME', value: 'aisachanka')]
	copyArtifacts filter: 'jobs.groovy', projectName: 'MNTLAB-aisachanka-child1-build-job'
    }
    stage ("Packaging and Publishing results") {
        archiveArtifacts 'jobs.groovy, Jenkinsfile, helloworld-ws/target/helloworld-ws.war'
    }
}
