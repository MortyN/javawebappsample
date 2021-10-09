import groovy.json.JsonSlurper

def getFtpPublishProfile(def publishProfilesJson) {
  def pubProfiles = new JsonSlurper().parseText(publishProfilesJson)
  for (p in pubProfiles)
    if (p['publishMethod'] == 'FTP')
      return [url: p.publishUrl, username: p.userName, password: p.userPWD]
}

podTemplate(yaml: '''
    apiVersion: v1
    kind: Pod
    spec:
      containers:
      - name: maven
        image: maven:3.8.1-jdk-8
        command:
        - sleep
        args:
        - 99d
        tty: true
        volumeMounts:
        - mountPath: '/opt/app/shared'
          name: sharedvolume
      - name: azurecli
        image: mcr.microsoft.com/azure-cli:latest
        command:
        - sleep
        args:
        - 99d
        tty: true
        volumeMounts:
        - mountPath: '/opt/app/shared'
          name: sharedvolume
      volumes:
      - name: sharedvolume
        emptyDir: {}

''') {
    node(POD_LABEL) {
        stage('Get a Maven project') {
            checkout scm
            container('maven') {
                stage('Build a Maven project') {
                sh 'mvn clean package'
                }
            }
        }
        stage('Deploying Maven project') {
            def resourceGroup = 'rg-app-service'
            def webAppName = 'app-service-ci'
            def pubProfilesJson = sh script: "az webapp deployment list-publishing-profiles -g $resourceGroup -n $webAppName", returnStdout: true
            def ftpProfile = getFtpPublishProfile pubProfilesJson
            container('azurecli') {
                sh 'az'
            }
        }
    }
}