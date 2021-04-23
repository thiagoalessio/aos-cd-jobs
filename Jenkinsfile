#!/usr/bin/env groovy

node {
    checkout scm
    def build = load("build.groovy")
    def commonlib = build.commonlib
    def slacklib = commonlib.slacklib

    commonlib.describeJob("ocp4", """
        <h2>Build OCP 4.y components incrementally</h2>
        <b>Timing</b>: Usually run automatically from merge_ocp.
        Humans may run as needed. Locks prevent conflicts.

        In typical usage, scans for changes that could affect package or image
        builds and rebuilds the affected components.  Creates new plashets if
        the automation is not frozen or if there are RPMs that are built in this run, 
        and runs other jobs to sync builds to nightlies, create
        operator metadata, and sweep bugs and builds into advisories.

        May also build unconditionally or with limited components.
    """)


    // Expose properties for a parameterized build
    properties(
        [
            disableResume(),
            buildDiscarder(
                logRotator(
                    artifactDaysToKeepStr: '365',
                    daysToKeepStr: '365')),
            [
                $class: 'ParametersDefinitionProperty',
                parameterDefinitions: [
                    commonlib.dryrunParam(),
                    commonlib.mockParam(),
                    commonlib.ocpVersionParam('BUILD_VERSION', '4'),
                    string(
                        name: 'NEW_VERSION',
                        description: '(Optional) version for build instead of most recent\nor "+" to bump most recent version',
                        defaultValue: "",
                        trim: true,
                    ),
                    booleanParam(
                        name: 'FORCE_BUILD',
                        description: 'Build regardless of whether source has changed',
                        defaultValue: false,
                    ),
                    booleanParam(
                        name: 'FORCE_MIRROR_STREAMS',
                        description: 'Ensure images:mirror-streams runs after this build, even if it is a small batch',
                        defaultValue: false,
                    ),
                    choice(
                        name: 'BUILD_RPMS',
                        description: 'Which RPMs are candidates for building? "only/except" refer to list below',
                        choices: [
                            "all",
                            "only",
                            "except",
                            "none",
                        ].join("\n"),
                    ),
                    string(
                        name: 'RPM_LIST',
                        description: '(Optional) Comma/space-separated list to include/exclude per BUILD_RPMS (e.g. openshift,openshift-kuryr)',
                        defaultValue: "",
                        trim: true,
                    ),
                    choice(
                        name: 'BUILD_IMAGES',
                        description: 'Which images are candidates for building? "only/except" refer to list below',
                        choices: [
                            "all",
                            "only",
                            "except",
                            "none",
                        ].join("\n"),
                    ),
                    string(
                        name: 'IMAGE_LIST',
                        description: '(Optional) Comma/space-separated list to include/exclude per BUILD_IMAGES (e.g. logging-kibana5,openshift-jenkins-2)',
                        defaultValue: "",
                        trim: true,
                    ),
                    booleanParam(
                        name: 'SCRATCH',
                        description: 'Run scratch builds (only unrelated images, no children)',
                        defaultValue: false,
                    ),
                    commonlib.suppressEmailParam(),
                    string(
                        name: 'MAIL_LIST_SUCCESS',
                        description: '(Optional) Success Mailing List\naos-cicd@redhat.com,aos-qe@redhat.com',
                        defaultValue: "",
                        trim: true,
                    ),
                    string(
                        name: 'MAIL_LIST_FAILURE',
                        description: 'Failure Mailing List',
                        defaultValue: [
                            'aos-art-automation+failed-ocp4-build@redhat.com'
                        ].join(','),
                        trim: true
                    ),
                    string(
                        name: 'SPECIAL_NOTES',
                        description: '(Optional) special notes to include in the build email',
                        defaultValue: "",
                        trim: true,
                    ),
                ]
            ],
        ]
    )

    currentBuild.description = ""
    scratch = params.SCRATCH
    stage("set build type") { build.setBuildType() }
    stage("stop here") {
        echo scratch
        fail
    }
}
