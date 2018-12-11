node ("${SLAVE}") { 
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
 //     sh "/apache-maven-3.5.4/bin/mvn pre-integration-test -f helloworld-ws/pom.xml"
       
    }
 //   stage('nexus') {
       // nexusPublisher nexusInstanceId: 'nexus3', nexusRepositoryId: 'maven-releases', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: 'helloworld-ws/target/helloworld-ws.war']], mavenCoordinate: [artifactId: 'helloworld-ws', groupId: 'helloworld', packaging: 'jar', version: '$BUILD_NUMBER']]]
        
//    }
    
   stage('Triggering job and fetching artefact after finishing') {
    build job: 'MNTLAB-kkalesnikava-child1-build-job', parameters: [string(name: 'Branch', value: 'kkalesnikava')]
   // copyArtifacts filter: 'jobs.groovy', projectName: 'MNTLAB-kkalesnikava-child1-build-job'
  
     //  }
       }
   }

