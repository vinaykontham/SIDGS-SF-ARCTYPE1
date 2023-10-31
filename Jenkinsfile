#!groovy

def exampleApi = "mvn archetype:generate " +
        "-DarchetypeGroupId=com.hdfc.sharedflow.archetype " +
        "-DarchetypeArtifactId=sf " +
        "-DarchetypeVersion=1.0.0-SNAPSHOT " +
        "-DgroupId=com-hdfc-sharedflow " +
        "-DartifactId=${params.artifactId} " +
        "-Dpackage=com.hdfc.sharedflow " +
        //"-DteamName=${params.teamName} " +
       // "-DprojectName=${params.projectName} " +
        "-DinteractiveMode=false"

node {

stage('Clean') {sh ("mvn clean")}

    stage("arche-type-build-deploy") {
        checkout scm
        if (!isUnix()) {
            bat("mvn install")
        } else {
            sh("mvn install")
        }
    }

    stage("arche-type-generate") {
        dir('target') {
            if (!isUnix()) {
                bat("rd /S /Q avengers")
                bat("${exampleSharedFlow}")
            } else {
                sh("rm -rf avengers")
                sh("${exampleSharedFlow}")
            }
        }
    }

    echo "Load and Run the Dev Pipeline of the Generated SharedFlow"

    // Clean out maven
//    deleteDir("target")

   // @Library("cicd@master") _
   // dir("target/avengers/edge") {
      //  edgeProxyCiModelOnePipeline "NO-SCM", env.BUILD_NUMBER
    //}
}
