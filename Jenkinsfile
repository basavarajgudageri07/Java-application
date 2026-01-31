pipeline{
   agent any
   environment{
    IMAGE_NAME = "babugudageri/java:${GIT_COMMIT"
   }
   tools {
    jdk 'java-17'
    maven 'maven'
   }
   stages{
    stage('git-checkout'){
        steps{
            git branch: 'main', url: 'https://github.com/basavarajgudageri07/Java-application.git'
        }
    }
    stage('compile'){
        steps{
            sh 'mvn compile'
        }
    }
    stage('build'){
        steps{
           sh 'mvn clean package'
        }
    }
    stage('sonarqube-stage'){
        stpes{
            sh """
            node {
             stage('SCM') {
             checkout scm
            }
             stage('SonarQube Analysis') {
            def mvn = tool 'Default Maven';
             withSonarQubeEnv() {
             sh "${mvn}/bin/mvn clean verify org.sonarsource.scanner.maven:sonar-maven-plugin:sonar -Dsonar.projectKey=Java -Dsonar.projectName='Java'"
            }
          }
       }
            """
        }
    }
        stage('docker build'){
            steps{
                sh 'docker build -t ${IMAGE_NAME} .'
            }
        }
        stage('login to DockerHub'){
            stpes{
                script{
                 withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh "echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin"
                }
            }
        }
    }
    stage('Deploy to KIND'){
        stage('Deploy to Kubernetes') {
            steps {
                echo "⚙️ Deploying to Kubernetes"
                withKubeConfig(
                    credentialsId: 'kubecredID'
                ) {
                    sh '''
                        echo "Applying Deployment..."
                        kubectl apply -f Deployment.yml
                        echo "Applying Service..."
                        kubectl apply -f Service.yml
                    '''
    }
    
   }
        }
    }
}
}
