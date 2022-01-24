boolean isTriggeredByTimer = "${currentBuild.getBuildCauses('hudson.triggers.TimerTrigger$TimerTriggerCause')}" != '[]'
boolean isTriggeredByUser = "${currentBuild.getBuildCauses('hudson.model.Cause$UserIdCause')}" != '[]'
String cronString = BRANCH_NAME == 'main' ? 'H 23 * * *' : ''
echo "isTriggeredByTimer = ${isTriggeredByTimer}"
echo "isTriggeredByUser = ${isTriggeredByUser}"

node {

    properties([
        pipelineTriggers([cron(cronString)]),
        buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '14', numToKeepStr: ''))
    ])

    stage('Build All Jobs') {
        if (isTriggeredByTimer || isTriggeredByUser) {

            def hierarchy = [
                ['Palladio-Supporting-Prolog', 'Palladio-Supporting-DataFlowDiagram'],
                ['Palladio-Supporting-Problog'],
                ['Palladio-Supporting-Prolog4J'],
                ['Palladio-Supporting-DataFlowDiagramConfidentiality'],
                ['Palladio-Addons-DataFlowConfidentiality', 'Palladio-Supporting-DataFlowConfidentialityQueryDSL'],
                ['Palladio-Addons-DataFlowConfidentialityQueryDSL', 'Palladio-Addons-ContextConfidentiality-Metamodel'],
                ['Palladio-Addons-ContextConfidentiality-Analysis'],
                ['FluidTrust-Build-Updatesite'],
                ['Palladio-Bench-Product-DataFlowConfidentiality', 'Palladio-Addons-DataFlowConfidentiality-DSE'],
                ['Palladio-Bench-Product-AttackerPropagation']
            ]

            hierarchy.each { repoNames ->
                def buildJobs = [:]
                repoNames.each { repoName ->
                    buildJobs["${repoName}"] = {
                        build job: "FluidTrust/${repoName}/main", propagate: false
                    }
                }
                parallel buildJobs
            }

        } else {
            echo "Build not triggered by user or timer. Doing nothing."
        }
    }

}
