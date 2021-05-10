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
                    commonlib.shell(
                        script: """
                        rm -rf ./${params.VERSION}
                        mkdir -p ./${params.VERSION}
                        cd ./${params.VERSION}
                        brew download-build ${params.NVR}
                        tree
                        """
                    )
                }
            }
        }
        stage("extract binaries") {
            steps {
                dir("./${params.VERSION}") {
                    script {
                        commonlib.shell(
                            script: """
                            rpm2cpio *.aarch64.rpm | cpio -idm ./usr/bin/butane
                            mv ./usr/bin/butane ./butane_aarch64
                            rm -rf *.aarch64.rpm ./usr
                            """
                        )
                        commonlib.shell(
                            script: """
                            rpm2cpio *.noarch.rpm | cpio -idm ./usr/share/butane-redistributable/*
                            mv ./usr/share/butane-redistributable/* .
                            rm -rf *.noarch.rpm ./usr
                            """
                        )
                        commonlib.shell(
                            script: """
                            rpm2cpio *.ppc64le.rpm | cpio -idm ./usr/bin/butane
                            mv ./usr/bin/butane ./butane_ppc64le
                            rm -rf *.ppc64le.rpm ./usr
                            """
                        )
                        commonlib.shell(
                            script: """
                            rpm2cpio *.s390x.rpm | cpio -idm ./usr/bin/butane
                            mv ./usr/bin/butane ./butane_s390x
                            rm -rf *.s390x.rpm ./usr
                            """
                        )
                        commonlib.shell(
                            script: """
                            rpm2cpio *.x86_64.rpm | cpio -idm ./usr/bin/butane
                            mv ./usr/bin/butane ./butane_amd64
                            ln -s ./butane_amd64 ./butane
                            rm -rf *.x86_64.rpm ./usr
                            """
                        )
                    }
                }
                sh "tree ./${params.VERSION}"
            }
        }
    }
}
