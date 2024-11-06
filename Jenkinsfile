 node('ubuntu-Appserver-2')
{
    def app
    stage('Cloning Git')
    {
    /* Let's make sure we have the repository cloned to our workspace */
    checkout scm
    }

      stage('SCA-SAST-SNYK-TEST') 
      {
       agent 
       
       {
         label 'ubuntu-Appserver-2'
       }
       
         snykSecurity(
            snykInstallation: 'Snyk',
            snykTokenId: 'SNYKID',
            severity: 'critical'
         )
       }
    stage('SonarQube Analysis') {
       agent {
           label 'ubuntu-Appserver-2'
       }
           script{
               def scannerHome = tool 'SonarQubeScanner'
               withSonarQubeEnv('sonarqube') {
                   sh "${scannerHome}/bin/sonar-scanner \
                       -Dsonar.projectKey=snakeapp \
                       -Dsonar.sources=."
               }
           }
       }
     
    stage('Build-and-Tag')
    {
        /* This builds the actual image; 
        * This is synonymous to docker build on the command line */
        app = docker.build("benjast/sonarsnakegame")
    }
    stage('Post-to-dockerhub')
    {
        docker.withRegistry('https://registry.hub.docker.com', 'dockerhub_credentials2')
        {
         app.push("latest")
        }
    }

    stage('Pull-image-server')
    {
        sh "docker-compose down"
        sh "docker-compose up -d"
    }

}
