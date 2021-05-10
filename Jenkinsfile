pipeline {
    agent any
    options { disableResume() }

    parameters {
        string(
            name: "NVR",
            description: "NVR of the brew build",
            defaultValue: "",
            trim: true,
        )
        string(
            name: "VERSION",
            description: "Desired version name. Example: v0.11.0"
            defaultValue: "",
            trim: true,
        )
    }

    stages {
        stage("validate params") {
            steps {
                script {
                    if (!params.NVR) {
                        error "NVR must be specified"
                    }
                    if (!params.VERSION) {
                        error "VERSION must be specified"
                    }
                }
            }
        }
        stage("download build") {
            steps {
                sh """
                    rm -rf ./${params.VERSION} &&\
                    mkdir -p ./${params.VERSION}" &&\
                    cd ./${params.VERSION} &&\
                    brew download-build ${params.NVR} &&\
                    tree
                """
            }
        }
    }
}
