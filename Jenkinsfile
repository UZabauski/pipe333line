node('${SLAVE}') {
    stage('Checkout code') {
        steps {
            checkout scm
        }
    }

    stage('Build') {
        sh 'make'
    }

    stage('Test') {
        sh 'make check'
        junit 'reports/**/*.xml'
    }

    stage('Deploy') {
        sh 'make publish'
    }
}
