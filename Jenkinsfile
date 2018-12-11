#!groovy
String email_to = 'murashandme@gmail.com'


node {
    stage('git checkout') {
        String git_repo = 'https://github.com/MNT-Lab/pipe333line/'
        String timeStamp = new java.util.Date()
        try {
            git branch: 'umuraveika',
                    url: git_repo
        }
        catch (exc) {
            String error_string = "Git - Failed to connect to ${git_repo}"
            emailext body: error_string,
                    mimeType: 'text/html',
                    subject: "${error_string} at ${timeStamp}",
                    to: email_to
            error(error_string)
        }
    }
    stage('Build with maven - clean install') {
        String error_string = "Failed to build with maven - clean install"
        String timeStamp = new java.util.Date()
        def mvnHome = tool name: 'maven', type: 'maven'
        try {
            sh "${mvnHome}/bin/mvn -f helloworld-ws/pom.xml compile"
        }
        catch (exc) {
            emailext body: error_string,
                    mimeType: 'text/html',
                    subject: "${error_string} at ${timeStamp}",
                    to: email_to
            error(error_string)
        }
    }
    stage('Package with maven') {
        String error_string = "Failed to package with maven"
        String timeStamp = new java.util.Date()
        def mvnHome = tool name: 'maven', type: 'maven'
        try {
            sh "${mvnHome}/bin/mvn -Dmaven.test.failure.ignore -f helloworld-ws/pom.xml clean package"
        }
        catch (exc) {
            emailext body: error_string,
                    mimeType: 'text/html',
                    subject: "${error_string} at ${timeStamp}",
                    to: email_to
            error(error_string)
        }
    }
    stage('Testing') {
        def mvnHome = tool name: 'maven', type: 'maven'
        parallel(
                a: { sh "${mvnHome}/bin/mvn -Dmaven.test.failure.ignore pre-integration-test -f helloworld-ws/pom.xml" },
                b: { sh "${mvnHome}/bin/mvn -Dmaven.test.failure.ignore integration-test -f helloworld-ws/pom.xml" },
                c: { sh "${mvnHome}/bin/mvn -Dmaven.test.failure.ignore post-integration-test -f helloworld-ws/pom.xml" }
        )
    }
}