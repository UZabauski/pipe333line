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
      parallel(
          a: {sh 'mvn pre-integration-test -f helloworld-ws/pom.xml'},
          b: {sh 'mvn integration-test -f helloworld-ws/pom.xml'},
          c: {sh 'mvn post-integration-test -f helloworld-ws/pom.xml'}
)
       
    }
 //   stage('nexus') {
       // nexusPublisher nexusInstanceId: 'nexus3', nexusRepositoryId: 'maven-releases', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: 'helloworld-ws/target/helloworld-ws.war']], mavenCoordinate: [artifactId: 'helloworld-ws', groupId: 'helloworld', packaging: 'jar', version: '$BUILD_NUMBER']]]
        
//    }
    
   stage('Triggering job and fetching artefact after finishing') {
    build job: 'MNTLAB-kkalesnikava-child1-build-job', parameters: [string(name: 'Branch', value: 'kkalesnikava')]
    copyArtifacts filter: 'jobs.groovy', fingerprintArtifacts: true, projectName: 'MNTLAB-kkalesnikava-child1-build-job', selector: lastSuccessful()
  
    stage('Packaging and Publishing results') {
   
   sh 'tar -czf pipeline-kkalesnikava-${BUILD_NUMBER}.tar.gz jobs.groovy Jenkinsfile helloworld-ws/target/helloworld-ws.war'
   sh 'curl -v -u admin:admin123 -X POST "http://10.6.204.217:8081/service/rest/v1/components?repository=maven-releases" -F "maven2.groupId=helloworld" -F "maven2.artifactId=helloworld-ws" -F "maven2.version=55" -F "maven2.asset1=@pipeline-kkalesnikava-${BUILD_NUMBER}.tar.gz; type=application/x-webarchive" -F "maven2.asset1.extension=tar"'
        stage('Asking for manual approval') {
            input "Deploy to prod?"
        }
        stage('deploy to prod'){
        echo "deploying"
    }
            
            
            
        }
   }
       
       }
   }

