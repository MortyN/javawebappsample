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
                    echo $testvalue 
                    """
                }
            }
        }
    }
}