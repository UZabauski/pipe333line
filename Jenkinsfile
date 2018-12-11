node { 
    stage('Preparation (Checking out)') { 
        echo "Build step"
     checkout([$class: 'GitSCM', branches: [[name: '*/kkalesnikava']],
     userRemoteConfigs: [[url: 'https://github.com/MNT-Lab/pipe333line.git']]])
     
    }
    stage('Building code') { 
        echo "Building code"
        sh "mvn -version"
        sh "mvn install -f helloworld-ws/pom.xml"
  }
}
    stage('Deploy') { 
        echo "Deploy" 
    }
