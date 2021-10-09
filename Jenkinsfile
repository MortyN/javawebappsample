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
        stage('Deploying Maven project') {
            def resourceGroup = 'rg-app-service'
            def webAppName = 'app-service-ci'
            container('azurecli') {
                stage('azure login') {
                    withCredentials([
                        usernamePassword(credentialsId: 'azure-service-principal-credentials', passwordVariable: 'AZURE_CLIENT_SECRET', usernameVariable: 'AZURE_CLIENT_ID'),
                        string(credentialsId: 'azure-tenant-id', variable: 'AZURE_TENANT_ID'),
                        string(credentialsId: 'azure-subscription-id', variable: 'AZURE_SUBSCRIPTION_ID')
                        ]) {
                        sh '''
                            az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
                            az account set -s $AZURE_SUBSCRIPTION_ID
                            '''
                        }
                }
                stage('deploy') {
                    def pubProfilesJson = sh script: "az webapp deployment list-publishing-profiles -g $resourceGroup -n $webAppName", returnStdout: true
                    def ftpProfile = getFtpPublishProfile pubProfilesJson
                    sh """
                          sh "curl -T target/sample.war $ftpProfile.url/webapps/ROOT.war -u '$ftpProfile.username:$ftpProfile.password'" ; az logout
                    """
                }
            }
        }
    }
}