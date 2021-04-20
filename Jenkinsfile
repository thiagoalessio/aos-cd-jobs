#!/usr/bin/env groovy

@NonCPS
def getAllCauses() {
    currentBuild.rawBuild.getCauses().toString()
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
