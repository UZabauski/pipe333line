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
        sh 'tar -cvf pipeline-ikazlouski-${BUILD_NUMBER}.tar.gz helloworld-ws.war jobs.groovy Jenkinsfile'
        archiveArtifacts 'pipeline-ikazlouski-${BUILD_NUMBER}.tar.gz'
        sh "curl -v --user 'admin:admin123' --upload-file pipeline-ikazlouski-${BUILD_NUMBER}.tar.gz http://192.168.50.4:8081/repository/myraw/pipeline-ikazlouski-${BUILD_NUMBER}.tar.gz"
    }
    stage ('Deployment') {
        sh '''cat > Dockerfile <<EOF
FROM openjdk:7-jre
ARG TOMCAT_ID=1000
ARG TOMCAT_HOME=/opt/tomcat
ARG TOMCAT_VERSION=7.0.91
RUN wget -O /opt/tomcat.zip https://archive.apache.org/dist/tomcat/tomcat-7/v${TOMCAT_VERSION}/bin/apache-tomcat-${TOMCAT_VERSION}.zip && \
cd $(dirname ${TOMCAT_HOME}) && unzip tomcat.zip && \
mv apache-tomcat-${TOMCAT_VERSION} tomcat && \
rm -f /opt/tomcat/tomcat.zip && \
useradd tomcat -u ${TOMCAT_ID} --no-create-home -d ${TOMCAT_HOME} && \
chown -R tomcat: ${TOMCAT_HOME}
USER tomcat
ENV CATALINA_HOME=${TOMCAT_HOME}
LABEL maintainer="Ihar Kazlouski"
COPY helloworld-project/helloworld-ws/target/helloworld-ws.war /opt/tomcat/webapps/
EXPOSE 8090
CMD bash /opt/tomcat/bin/catalina.sh run
EOF'''
     }
 }                   

