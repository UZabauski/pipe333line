node{
    stage ("initialize") {
        sh '''
        echo "PATH = ${PATH}"
        echo "M2_HOME = ${M2_HOME}"
    '''
    }
    stage('git checkout'){
        git branch: 'umuraveika', 
        url: 'https://github.com/MNT-Lab/pipe333line/'
    }   
}