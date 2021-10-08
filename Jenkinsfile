podTemplate(label: 'mypod', containers: [
    containerTemplate(name: 'maven', image: 'maven:3.3.9-jdk-8-alpine', command: 'cat', ttyEnabled: true, envVars: [
        envVar(key: "resourceGroup", value: "rg-app-service")
        envVar(key: "webAppName", value: "app-service-ci")
    ]),
    containerTemplate(name: 'docker', image: 'docker', command: 'cat', ttyEnabled: true)
  ],
  volumes: [
    hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'),
  ]
  )   {
        node('mypod') {
            stage('Build') {
                checkout scm
                container('maven') {
                sh """
                        mvn -version ; mvn clean package
                                                        """
                }
            }
            stage('Deploy') {
                def testvalue = "piffting"
                container('docker') {               
                sh """
                   echo $resourceGroup ; echo $testvalue 
                """
                }
            }
        }
    }