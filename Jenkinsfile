#!/usr/bin/env groovy

@NonCPS
def triggeredByHuman?() {
    currentBuild.rawBuild.getCauses().collect {
        it.getClass().getCanonicalName().tokenize('.').last()
    }.contains('UserIdCause')
}

pipeline {
    agent any
    options { disableResume() }

    stages {
        stage("Testing") {
            steps {
                script {
                    echo triggeredByHuman?
                }
            }
        }
    }
}
