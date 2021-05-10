node {
    checkout scm
    commonlib = load("pipeline-scripts/commonlib.groovy")
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
        string(
            name: "ARCHES",
            description: "Arches to extract from the brew build.<br/>" +
                         "Defaults to all contained in the RPM.<br/>" +
                         "windows-amd64 and darwin-amd64 are present in arch \"noarch\".",
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
                    arches = commonlib.cleanCommaList(params.ARCHES).split(",")
                    def archesParams = arches.collect { "--arch=${it}" }.join(" ")

                    commonlib.shell(
                        script: """
                        rm -rf ./${params.VERSION}
                        mkdir -p ./${params.VERSION}
                        cd ./${params.VERSION}
                        brew download-build ${params.NVR} ${archesParams}
                        tree
                        """
                    )
                }
            }
        }
        stage("extract binaries") {
            steps {
                echo "${arches}"
            }
        }
    }
}
