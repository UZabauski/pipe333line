node ("${SLAVE}"){

    stage ('Preparation') { 
    checkout changelog: false, scm: [$class: 'GitSCM', branches: [[name: '*/ikazlouski']],
      doGenerateSubmoduleConfigurations: false, extensions: [],
      submoduleCfg: [],
      userRemoteConfigs: [[url: 'https://github.com/MNT-Lab/pipe333line.git']]]
     } 

    stage('Building code') {
        sh '/opt/maven/bin/mvn clean compile -f helloworld-ws/pom.xml package'
	sh '/opt/maven/bin/mvn package -f helloworld-ws/pom.xml package'
    }

    stage ("Testing") {
	parallel(
            a: {sh '/opt/maven/bin/mvn pre-integration-test -f helloworld-ws/pom.xml'},
	    b: {sh '/opt/maven/bin/mvn integration-test -f helloworld-ws/pom.xml'},
	    c: {sh '/opt/maven/bin/mvn post-integration-test -f helloworld-ws/pom.xml'}
	)
    }

    stage ('Trigger job, fetch artifact'){
        build job: 'MNTLAB-ikazlouski-child1-build-job' ,
                parameters: [[$class: 'StringParameterValue', name: 'BRANCH_NAME', value: "ikazlouski"]]
        copyArtifacts filter: 'ikazlouski_dsl_script.tar.gz	',
                projectName: 'MNTLAB-ikazlouski-child1-build-job'
    }

    stage('Packaging and Publishing results'){
        sh 'tar -xvf ikazlouski_dsl_script.tar.gz'
        sh 'cp -f helloworld-ws/target/helloworld-ws.war .'
        sh 'tar -cvf pipeline-ikazlouski-$BUILD_NUMBER.tar.gz helloworld-ws.war jobs.groovy Jenkinsfile'
        sleep(5)
        
        archiveArtifacts 'pipeline-ikazlouski-$BUILD_NUMBER.tar.gz'
	sh 'curl -v --user 'admin:admin123' --upload-file pipeline-ikazlouski-$BUILD_NUMBER.tar.gz http://192.168.50.4:8081/repository/myraw/pipeline-ikazlouski-$BUILD_NUMBER.tar.gz'
    }
}
