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
        env:
          - name: resourceGroup
            value: rg-app-service
          - name: webAppName
            value: app-service-ci 
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
            def testvalue = 'piffting'
            container('maven') {
                stage('azure deployment') {
                    sh """
                    echo $resourceGroup ; echo $testvalue 
                    """
                }
            }
        }
    }
}

// podTemplate(yaml: """
// apiVersion: v1
// kind: Pod
// spec:
//   containers:
//   - name: maven
//     image: maven:3.8.3-jdk-8-openj9
//     env:
//       - name: resourceGroup
//         value: rg-app-service
//       - name: webAppName
//         value: app-service-ci       
// """
// )   {
//         node(POD_LABEL) {
//             stage('Build') {
//                 checkout scm
//                 container('maven') {
//                 sh """
//                     mvn -version
//                                                         """
//                 }
//             }
//         }
//     }

// podTemplate(label: 'mypod', containers: [
//     containerTemplate(name: 'maven', image: 'maven:3.3.9-jdk-8-alpine', command: 'cat', ttyEnabled: true, envVars: [
//         envVar(key: "resourceGroup", value: "rg-app-service")
//         envVar(key: "webAppName", value: "app-service-ci")
//     ]),
//     containerTemplate(name: 'docker', image: 'docker', command: 'cat', ttyEnabled: true)
//   ],
//   volumes: [
//     hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'),
//   ]
//   )   {
//         node('mypod') {
//             stage('Build') {
//                 checkout scm
//                 container('maven') {
//                 sh """
//                         mvn -version ; mvn clean package
//                                                         """
//                 }
//             }
//             stage('Deploy') {
//                 def testvalue = "piffting"
//                 container('docker') {               
//                 sh """
//                    echo $resourceGroup ; echo $testvalue 
//                 """
//                 }
//             }
//         }
//     }