node{
    
    def mavenHome
    def mavenCMD
    
    
    def tagName
    
    stage('prepare enviroment'){
        echo 'initialize all the variables'
        mavenHome = tool name: 'maven' , type: 'maven'
        mavenCMD = "${mavenHome}/bin/mvn"
        
        tagName="5.0"
    }
    
    stage('git code checkout'){
        try{
            echo 'checkout the code from git repository'
            git 'https://github.com/raghu949/star-agile-insurance-project.git'
        }
        catch(Exception e){
            echo 'Exception occured in Git Code Checkout Stage'
            currentBuild.result = "FAILURE"
            emailext body: '''Dear All,
            The Jenkins job ${JOB_NAME} has been failed. Request you to please have a look at it immediately by clicking on the below link. 
            ${BUILD_URL}''', subject: 'Job ${JOB_NAME} ${BUILD_NUMBER} is failed', to: 'raghurama2@gmail.com'
        }
    }
    
    stage('Build the Application'){
        echo "Cleaning... Compiling...Testing... Packaging..."
        //sh 'mvn clean package'
        sh "${mavenCMD} clean package"        
    }
    stage('Containerize the application'){
        echo 'Creating Docker image'
        sh "docker build -t rampracticedocker/insure-me:${tagName} ."
    }
     stage('Pushing it ot the DockerHub'){
        echo 'Pushing the docker image to DockerHub'
        withCredentials([string(credentialsId: 'dock-password', variable: 'dockerHubPassword')]) {
        sh "docker login -u raghurama2@gmail.com -p ${dockerHubPassword}"
        sh "docker push rampracticedocker/insure-me:${tagName}"
            
        }
        
    }
    stage('Configure and Deploy to the test-server'){
        
         ansiblePlaybook become: true, credentialsId: 'ansible-key', disableHostKeyChecking: true, installation: 'ansible', inventory: '/etc/ansible/hosts', playbook: 'ansible-playbook.yml', vaultTmpPath: ''
    }
    
}





