node {
    def giturl = 'https://github.com/MNT-Lab/p323line.git'

    stage('Clone sources') {
        checkout([$class: 'GitSCM', branches: [[name: '*/ikazlouski']], doGenerateSubmoduleConfigurations: false,
                  extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: giturl]]])

    }
}
