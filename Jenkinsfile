pipeline {
    agent { docker { image 'maven:3.6.3-openjdk-11' } }
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
              script {
                step ([$class: 'CopyArtifact',
                    projectName: '../${JOB_BASE_NAME}',
                    filter: "${JOB_BASE_NAME}*.jar",
                    target: '.']);
              }
            }
        }       
    }      
    post {
        success {
             sh 'java -jar ${JOB_BASE_NAME}*.jar'
        }
    }
}
