node {
    stage ('Preparation') 
    checkout changelog: false, scm: [$class: 'GitSCM', branches: [[name: '*/ikazlouski']],
      doGenerateSubmoduleConfigurations: false, extensions: [],
      submoduleCfg: [],
      userRemoteConfigs: [[url: 'https://github.com/MNT-Lab/pipe333line.git']]]
      
    stage('Maven build') {
    withMaven(jdk: 'java', maven: 'maven') {
    sh 'mvn -f helloworld-ws/pom.xml package'
    }
  }
}
