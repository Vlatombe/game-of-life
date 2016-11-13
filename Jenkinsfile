#!groovy

properties([
   [$class: 'BuildDiscarderProperty',
      strategy: [$class: 'LogRotator', numToKeepStr: '10', artifactNumToKeepStr: '10']
   ]
])

docker.image('cloudbees/java-build-tools:1.0.0').inside {

    checkout scm

    stage 'Build Web App' {
        withMaven(mavenSettingsConfig: 'maven-settings-for-gameoflife') {
            sh "mvn clean source:jar javadoc:javadoc checkstyle:checkstyle pmd:pmd findbugs:findbugs package"

            step([$class: 'ArtifactArchiver', artifacts: 'gameoflife-web/target/*.war'])
            step([$class: 'WarningsPublisher', consoleParsers: [[parserName: 'Maven']]])
            step([$class: 'JUnitResultArchiver', testResults: '**/target/surefire-reports/TEST-*.xml'])
            step([$class: 'JavadocArchiver', javadocDir: 'gameoflife-core/target/site/apidocs/'])

            // Use fully qualified hudson.plugins.checkstyle.CheckStylePublisher if JSLint Publisher Plugin or JSHint Publisher Plugin is installed
            step([$class: 'hudson.plugins.checkstyle.CheckStylePublisher', pattern: '**/target/checkstyle-result.xml'])
            // In real life, PMD and Findbugs are unlikely to be used simultaneously
            step([$class: 'PmdPublisher', pattern: '**/target/pmd.xml'])
            step([$class: 'FindBugsPublisher', pattern: '**/findbugsXml.xml'])
            step([$class: 'AnalysisPublisher'])
        }
    }
}
