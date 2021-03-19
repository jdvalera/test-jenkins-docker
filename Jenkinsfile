node {
    def app
    def image

    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace */

        checkout scm
    }

    stage('Build image') {
        /* This builds the actual image; synonymous to
         * docker build on the command line */

        app = docker.build("jdvalera/hellonode")
    }

    stage('Test image') {
        /* Ideally, we would run a test framework against our image.
         * For this example, we're using a Volkswagen-type approach ;-) */

        app.inside {
            sh 'echo "Tests passed"'
        }
    }

    stage('Push image') {
        /* Finally, we'll push the image with two tags:
         * First, the incremental build number from Jenkins
         * Second, the 'latest' tag.
         * Pushing multiple tags is cheap, as all the layers are reused. */
        docker.withRegistry('https://registry.hub.docker.com', 'DOCKER-HUB-CREDENTIALS') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }
    }

    stage('Pull image') {
        docker.withRegistry('https://registry.hub.docker.com', 'DOCKER-HUB-CREDENTIALS') {
            image = docker.image("jdvalera/hellonode:${env.BUILD_NUMBER}")
            image.pull()
            image.run()
        }
    }

    stage('Run image') {
            sh 'docker container stop $(docker ps -a -q)'
            sh 'docker rm -f $(docker ps -a -q)'
            image.run()
    }
}