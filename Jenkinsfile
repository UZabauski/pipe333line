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
}
