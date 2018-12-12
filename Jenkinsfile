node ("${SLAVE}") { 
    
    stage('Preparation (Checking out)') {
        echo "checkout scm"
        checkout scm
    }
    
    stage('Building code') { 
        echo "Building code"
        tool name: 'mavenLocal', type: 'maven'
        tool name: 'java8', type: 'jdk'
        sh "mvn package -f helloworld-ws/pom.xml "
    }

    stage('Testing') { 
        echo "Testing"
        parallel(
            a: {sh 'mvn pre-integration-test -f helloworld-ws/pom.xml'},
            b: {sh 'mvn integration-test -f helloworld-ws/pom.xml'},
            c: {sh 'mvn post-integration-test -f helloworld-ws/pom.xml'}
        )
    }
    
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
        
    stage ("Deployment") {
        echo "Deploy"
        sh '''cat > Dockerfile <<EOF
FROM tomcat
RUN curl -u admin:admin123 -o pipeline-kkalesnikava-${BUILD_NUMBER}.tar.gz http://10.6.204.217:8081/repository/maven-releases/helloworld/helloworld-ws/55/helloworld-ws-55.tar -L && \
tar -xvf pipeline-kkalesnikava-${BUILD_NUMBER}.tar.gz && \
mv helloworld-ws/target/helloworld-ws.war /usr/local/tomcat/webapps
CMD bash /usr/local/tomcat/bin/catalina.sh run
EOF'''
        sh 'docker build -t tomcat_kkalesnikava .'
        sh 'docker tag tomcat_kkalesnikava 10.6.204.217:8082/kkalesnikava-hello-world:${BUILD_NUMBER}'
        sh 'docker push 10.6.204.217:8082/kkalesnikava-hello-world:${BUILD_NUMBER}'
    }     
        
        stage('Download') { 
            sh 'docker pull 10.6.204.217:8082/kkalesnikava-hello-world:${BUILD_NUMBER}'
            sh 'docker run -d -p 9090:8080 tomcat_kkalesnikava'
        } 
        stage('Email') {
            emailext attachLog: true, body: 'Test mail', subject: 'Pipeline-${BUILD_NUMBER}', to: 'kkoles556@gmail.com'
        }
        
    }
    }
}
   

