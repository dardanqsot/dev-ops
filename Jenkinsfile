pipeline {
    agent any

    tools {
        maven 'maven3.8.8'
    }
    stages {
        // stage('Checkout') {
        //     steps {
        //         git 'https://github.com/devops-mitocode/spring-petclinic-rest.git'
        //     }
        // }
        // stage('Compile') {
        //     steps {
        //         sh 'mvn clean compile -B -ntp'
        //     }
        // }
        // stage('Test') {
        //     steps {
        //         sh 'mvn test -B -ntp'
        //     }
        //     post {
        //         success {
        //             jacoco()
        //             junit 'target/surefire-reports/*.xml'
        //         }
        //         always {
        //             echo 'siempre se ejecuta'
        //         }
        //         failure {
        //             echo 'si fallan los tests'
        //         }
        //     }
        // }
        stage('Package') {
            steps {
                sh 'mvn clean package -DskipTests -B -ntp'
            }
        }
        /*
        stage('Sonarqube') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh 'mvn sonar:sonar -B -ntp'
                }
            }
        }
        stage("Quality Gate") {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage('Artifactory') {
            steps {
                script {

                    // Forma 2 - File Spec

                    sh 'env | sort'

                    def pom = readMavenPom file: 'pom.xml'
                    println pom

                    def server = Artifactory.server 'artifactory'
                    def repository = 'spring-petclinic-rest'

                    if("${GIT_BRANCH}" == 'origin/master'){
                        repository = repository + '-release'
                    } else {
                        repository = repository + '-snapshot'
                    }

                    def uploadSpec = """
                        {
                            "files": [
                                {
                                    "pattern": "target/.*.jar",
                                    "target": "${repository}/${pom.groupId}/${pom.artifactId}/${pom.version}/",
                                    "regexp": "true"
                                }
                            ]
                        }
                    """
                    server.upload spec: uploadSpec

                }
            }
        }
        stage('Nexus') {
            steps {
                script {

                    sh 'env | sort'

                    def pom = readMavenPom file: 'pom.xml'
                    println pom

                    nexusPublisher nexusInstanceId: 'nexus',
                    nexusRepositoryId: 'spring-petclinic-rest-release',
                    packages: [[$class: 'MavenPackage',
                    mavenAssetList: [[classifier: '', extension: '', filePath: "target/${pom.artifactId}-${pom.version}.jar"]],
                    mavenCoordinate: [
                    groupId: "${pom.groupId}",
                    artifactId: "${pom.artifactId}",
                    packaging: 'jar',
                    version: "${pom.version}-${BUILD_NUMBER}"]]]

                }
            }
        }
        */
        stage("DockerHub"){
            steps{
                script {
                    /*
                    def pom = readMavenPom file: 'pom.xml'
                    def app = docker.build("danycenas/${pom.artifactId}:${pom.version}")

                    docker.withRegistry('https://registry.hub.docker.com/', 'dockerhub-credentials') {
                        app.push()
                        app.push('latest')
                    }
                    */
                    def pom = readMavenPom file: 'pom.xml'
                    def app = docker.build("docker-spring-petclinic-rest/${pom.artifactId}:${pom.version}")

                    docker.withRegistry('http://35.85.34.241:8081', 'artifactory-registry-credentials') {
                        app.push()
                        app.push('latest')
                    }
                }
            }
        }
    }
    post {
        success {
            archiveArtifacts artifacts: 'target/*.jar'
            deleteDir()
        }
    }
}