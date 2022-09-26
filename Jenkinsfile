pipeline{
    agent any
    tools {
        maven 'Maven-3.8.6'
    }
    options {
        buildDiscarder logRotator(artifactDaysToKeepStr: '3', artifactNumToKeepStr: '3', daysToKeepStr: '3', numToKeepStr: '3')
        timestamps()
    }
    stages{
        stage("CheckOutCodeFromGitHub"){
            steps{
                git 'https://github.com/Sujana-Demo-Projects/java-web-app-docker.git'
            }
        }
        stage("BuildingPackage"){
            steps{
                sh "mvn clean package"
            }
        }
        stage("SourceCodeQualityTestingUsingSonarQube"){
            steps{
                sh "mvn sonar:sonar"
            }
        }
        stage("StoringPackageInJFrog"){
            steps{
                sh "mvn deploy"
            }
        }
        stage("CreatingDockerImage"){
            steps{
                sh "docker build -t sujanadevops/javaappdemo:${BUILD_NUMBER} ."
            }
        }
        stage("AuthenticatingDockerRegistry"){
            steps{
                sh "docker login -u sujanadevops -p Sujana@45"
            }
        }
        stage("PushingImagetoDockerRegistry"){
            steps{
                sh "docker push sujanadevops/javaappdemo:${BUILD_NUMBER}"
            }
        }
        stage('Anchore analyse') {  
            steps {  
                writeFile file: 'anchore_images', text: 'docker.io/sujanadevops/javaappdemo:${BUILD_NUMBER}'  
                anchore name: 'anchore_images'  
            }  
        }
        stage("ChangingVersionInDeployment.yaml"){
            steps{
                sh 'sed s/number/${BUILD_NUMBER}/g deployment.yml > demo.yaml'
            }
        }
        stage("DeployingAppInK8S"){
            steps{
                sh "kubectl apply -f demo.yaml"
            }
        }
    }
}
