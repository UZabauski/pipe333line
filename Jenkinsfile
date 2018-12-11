node('EPBYMINW2466') {
    stage("Preparation (Checking out)") {
    	checkout([$class: 'GitSCM', 
        branches: [[name: '*/askorkin']], 
        userRemoteConfigs: [[url: 'https://github.com/MNT-Lab/pipe333line.git']]])
    }
    stage ("Building code") {
        sh '/home/student/Programs/apache-maven-3.6.0/bin/mvn compile -f helloworld-ws/pom.xml'
        sh '/home/student/Programs/apache-maven-3.6.0/bin/mvn package -f helloworld-ws/pom.xml'
    }
    stage ("Testing") {
	parallel(
            a: {sh '/home/student/Programs/apache-maven-3.6.0/bin/mvn pre-integration-test -f helloworld-ws/pom.xml'},
	    b: {sh '/home/student/Programs/apache-maven-3.6.0/bin/mvn integration-test -f helloworld-ws/pom.xml'},
	    c: {sh '/home/student/Programs/apache-maven-3.6.0/bin/mvn post-integration-test -f helloworld-ws/pom.xml'}
	)
    }
}
