node ("${SLAVE}"){
    stage ('Preparation') { 
    checkout changelog: false, scm: [$class: 'GitSCM', branches: [[name: '*/ikazlouski']],
      doGenerateSubmoduleConfigurations: false, extensions: [],
      submoduleCfg: [],
      userRemoteConfigs: [[url: 'https://github.com/MNT-Lab/pipe333line.git']]]
     } 
    stage('Maven build') {
        sh '/opt/maven/bin/mvn complile-f helloworld-ws/pom.xml package'
   	sh '/opt/maven/bin/mvn package -f helloworld-ws/pom.xml package'
    }
}
