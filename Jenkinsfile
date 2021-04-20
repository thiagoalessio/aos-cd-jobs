#!/usr/bin/env groovy

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

@NonCPS
def getAllCauses() {
    return currentBuild.rawBuild.getClauses.toString()
}
