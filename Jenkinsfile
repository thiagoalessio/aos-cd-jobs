node {
    checkout scm
    def commonlib = load("pipeline-scripts/commonlib.groovy")
    commonlib.describeJob("butane_sync", """
        ------------------------------
        Sync Butane binaries to mirror
        ------------------------------
        Butane (formerly the Fedora CoreOS Config Transpiler, FCCT)
        http://mirror.openshift.com/pub/openshift-v4/clients/butane/

        Timing: This is only ever run by humans, upon request.
    """)
}

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
            description: "Desired version name. Example: v0.11.0",
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
                script {
                    commonlib.shel(
                        script: """
                        rm -rf ./${params.VERSION}
                        mkdir -p ./${params.VERSION}"
                        cd ./${params.VERSION}
                        brew download-build ${params.NVR}
                        tree
                        """
                    )
                }
            }
        }
    }
}
