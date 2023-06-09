pipeline {
    agent { label 'JDK_17' }
    triggers { pollSCM ('* * * * *') }
    parameters {
        choice(name: 'MAVEN_GOAL', choices: ['package', 'install', 'clean'], description: 'Maven Goal')
    }
    stages {
        stage('vcs') {
            steps {
                git url: 'https://github.com/khajadevopsmarch23/spring-petclinic.git',
                    branch: 'dev'
            }
        }
        stage('package') {
            tools {
                jdk 'JDK_17'
            }
            steps {
                sh "mvn clean"
                sh "mvn ${params.MAVEN_GOAL}"
            }
        }
        stage('post build') {
            steps {
                archiveArtifacts artifacts: '**/target/spring-petclinic-3.0.0-SNAPSHOT.jar',
                                 onlyIfSuccessful: true
                junit testResults: '**/surefire-reports/TEST-*.xml'
                stash name: 'spc-jar',
                      includes: '**/target/spring-petclinic-3.0.0-SNAPSHOT.jar'
            }
        }

        stage('ansible') {
            agent { label 'JDK_8' }
            steps {
                unstash name: 'spc-jar'
            }
        }
    }
}