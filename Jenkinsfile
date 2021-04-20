#!/usr/bin/env groovy

@NonCPS
def getAllCauses() {
    currentBuild.rawBuild.getCauses().collect {
        it.getClass().getCanonicalName().tokenize('.').last()
    }
}

pipeline {
    agent any
    options { disableResume() }

    stages {
        stage("Testing") {
            steps {
                script {
                    echo getAllCauses()
                }
            }
        }
    }
}
