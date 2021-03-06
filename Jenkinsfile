properties([
    parameters([
        gitParameter(branch: '',
                     branchFilter: 'origin/(.*)',
                     defaultValue: 'master',
                     description: '',
                     name: 'gh-pages',
                     quickFilterEnabled: false,
                     selectedValue: 'NONE',
                     sortMode: 'NONE',
                     tagFilter: '*',
                     type: 'PT_BRANCH')
    ])
])

node {
    // Get Artifactory server instance, defined in the Artifactory Plugin administration page.
    def server = Artifactory.server "jfrogartifactory"
    // Create an Artifactory Maven instance.
    def rtMaven = Artifactory.newMavenBuild()
    def buildInfo
    parameters {
    gitParameter branchFilter: 'origin/(.*)', defaultValue: 'master', name: 'gh-pages', type: 'PT_BRANCH'
  }
    
 rtMaven.tool = "Maven"

    stage('Clone sources') {
        git branch: "${params.BRANCH}" url: 'https://github.com/sandeeprasath/onlinebookstore.git'
    }

    stage('Artifactory configuration') {
        // Tool name from Jenkins configuration
        rtMaven.tool = "Maven"
        // Set Artifactory repositories for dependencies resolution and artifacts deployment.
        rtMaven.deployer releaseRepo:'libs-release-local', snapshotRepo:'libs-snapshot-local', server: server
        rtMaven.resolver releaseRepo:'libs-release', snapshotRepo:'libs-snapshot', server: server
    }

    stage('Maven build') {
        buildInfo = rtMaven.run pom: 'pom.xml', goals: 'package'
    }

    stage('Publish build info') {
        server.publishBuildInfo buildInfo
        deploy adapters: [tomcat8(credentialsId: 'tomcatserver', path: '', url: 'http://18.217.199.8:8080/')], contextPath: '/adminlog', war: '**/*.war'
    }
    }
