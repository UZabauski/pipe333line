node ("${SLAVE}"){

    stage('Preparation (Checking out)') {
	echo "checkout scm"
	checkout scm
    }  
    
    stage('Building code') {
	def mvnHome = tool name: 'mavenLocal', type: 'maven'
	sh '${mvnHome}/mvn clean compile -f helloworld-ws/pom.xml package'
	sh '${mvnHome}/mvn package -f helloworld-ws/pom.xml package'
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
        sh 'tar -cvf pipeline-ikazlouski-${BUILD_NUMBER}.tar.gz helloworld-ws.war jobs.groovy Jenkinsfile'
        archiveArtifacts 'pipeline-ikazlouski-${BUILD_NUMBER}.tar.gz'
        sh "curl -v --user 'admin:admin123' --upload-file pipeline-ikazlouski-${BUILD_NUMBER}.tar.gz http://192.168.50.4:8081/repository/myraw/pipeline-ikazlouski-${BUILD_NUMBER}.tar.gz"
    }

    stage ('Asking for manual approval'){
        script {
           timeout(time: 1, unit: 'MINUTES') {
               input(id: "Deploy Gate", message: "Deploy ?", ok: 'Deploy')
                  }
               }
 }                   

    stage ('Deployment') {
        sh '''cat > Dockerfile <<EOF
FROM openjdk:7-jre
ARG TOMCAT_ID=1000
ARG TOMCAT_HOME=/opt/tomcat
ARG TOMCAT_VERSION=7.0.91
RUN curl -v -o /opt/tomcat.zip https://archive.apache.org/dist/tomcat/tomcat-7/v7.0.91/bin/apache-tomcat-7.0.91.zip && \
cd /opt && unzip tomcat.zip && \
mv apache-tomcat-7.0.91 tomcat && \
rm -f /opt/tomcat/tomcat.zip && \
useradd tomcat -u 1000 --no-create-home -d /opt/tomcat && \
chown -R tomcat: /opt/tomcat
USER tomcat
ENV CATALINA_HOME=/opt/tomcat
LABEL maintainer="Ihar Kazlouski"
COPY helloworld-ws.war /opt/tomcat/webapps/
EXPOSE 8090
CMD bash /opt/tomcat/bin/catalina.sh run
EOF'''
	sh "curl -v --user 'admin:admin123' -o pipeline-ikazlouski-${BUILD_NUMBER}.tar.gz http://192.168.50.4:8081/repository/myraw/pipeline-ikazlouski-${BUILD_NUMBER}.tar.gz"
	sh 'tar -xvf pipeline-ikazlouski-${BUILD_NUMBER}.tar.gz'
	sh 'docker build -t tmct .'
	sh 'docker run -d -p 8090:8080 tmct'
     }

 }                   

