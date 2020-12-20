pipeline {
    agent { docker { image 'maven:3.3.3' } }
    options {
        skipStagesAfterUnstable()
        buildDiscarder(logRotator(numToKeepStr: '10', artifactNumToKeepStr: '15'))    
    }    
    parameters {  
        //gitParameter(branchFilter: 'origin/(.*)', defaultValue: 'develop', name: 'BRANCH', type: 'PT_BRANCH_TAG')
        string(name: 'BRANCH', defaultValue: 'develop', description: '') 
        string(name: 'VERSION', defaultValue: '1.0.0', description: '') 
        choice(name: 'LOG_LEVEL', choices: ['ERROR', 'INFO', 'DEBUG'], description: '')
    }
    stages {
        stage('Wipe') {
            steps {
                cleanWs deleteDirs: true, notFailBuild: true
            }
        }
        stage('build') {
            steps {
                sh 'mvn --version'
            }
        }        
        stage('Armando carpetas') {
            steps {
                script{
                    if(fileExists("/scripts/")){
                        sh 'mv scripts/* .'
                    }
                }
                sh 'mv target/*.jar .'
                sh 'rm original-*.jar'
            }
        }         
    }
    post {
        success {
             archiveArtifacts artifacts: '*.jar, *.bat'
        }
    }
}
