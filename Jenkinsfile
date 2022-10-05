node {
    def ARTIFACTORY_ID = 'artifactory'
    def ARTIFACTORY_URL = 'https://jilifrog.jfrog.io/'

    stage ('GIT Clone') {
        checkout scm
    }
    stage ('Config CLI Artifactory') {
        sh "curl -fL https://getcli.jfrog.io | sh"
        withCredentials([usernamePassword(credentialsId: 'ARTIFACTORY_CREDENTIALS', passwordVariable: 'ARTIFACTORY_PASSWORD', usernameVariable: 'ARTIFACTORY_USER')]) {
            sh "./jfrog config $ARTIFACTORY_ID --url=$ARTIFACTORY_URL --user=$ARTIFACTORY_USER --password=$ARTIFACTORY_PASSWORD"
        }
    }
    stage ('Maven Build & Publish' ) {
        sh "./jfrog config mvn 'clean install -DskipTests=true' configuration.yml --build-name=${env.JOB_NAME} --build-number=${env.BUILD_NUMBER}"
    }
    stage ('Unit Test') {
        sh "./jfrog config mvn test configuration.yml"
    }
    stage ('Get GIT info'){
        sh "./jfrog config build-add-git ${env.JOB_NAME} ${env.BUILD_NUMBER}"
    }
    stage ('Collect Build Env info'){
        sh "./jfrog config build-collect-env ${env.JOB_NAME} ${env.BUILD_NUMBER}"
    }
    stage ('Publish Build Info'){
        sh "./jfrog config build-publish ${env.JOB_NAME} ${env.BUILD_NUMBER}"
        cleanWs()
    }
}
