import java.text.SimpleDateFormat

node {
    parameters {
    	string(name: 'BRANCH', defaultValue: 'develop', description: '')
    }

    stage('Src Checkout') {
    	git branch: '${BRANCH}',
    		credentialsId: 'dokkl',
    		url: 'https://github.com/dokkl/hello-jib.git'
    }

    stage('Build') {
     	sh './gradlew bootJar'
    }

    stage('Build image') {

       app = docker.build("dokkl/hello-jib")
    }

    stage('Test image') {
        app.inside {
            sh 'echo "Tests passed"'
        }
    }

    stage('Push image') {

        docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
            app.push("${env.BUILD_NUMBER}")
        }
    }

    stage('Trigger ManifestUpdate') {
                echo "triggering updatemanifestjob"
                build job: 'updatemanifest', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
        }
}