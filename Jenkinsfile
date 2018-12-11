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
    }
}
