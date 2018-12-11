node { 
    stage('Preparation (Checking out)') { 
        echo "Build step"
     checkout([$class: 'GitSCM', branches: [[name: '*/kkalesnikava']],
     userRemoteConfigs: [[url: 'https://github.com/MNT-Lab/pipe333line.git']]])
     
    }
    stage('Building code') { 
        echo "Building code"

        sh "mvn compile -f helloworld-ws/pom.xml "
  }

    stage('Testing') { 
        echo "Testing"
      sh "mvn pre-integration-test -f helloworld-ws/pom.xml"
       
    }
    
//    stage('Triggering job and fetching artefact after finishing') {
//      echo "Testing"
  
     //  }
       }
