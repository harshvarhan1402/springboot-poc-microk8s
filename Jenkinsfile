pipeline {
    agent any
    stages {
        stage('list repo contents and working directory') {
            steps {
                script {
                    sh "ls"
                    sh "pwd"
                    sh "whoami"
                }
            }
        }
        stage('create build') {
            steps {
                script {
                    sh "./gradlew clean build"
                }
            }
        }
        stage('list the created build file') {
            steps {
                script {
                    sh "ls build/libs/"
                }
            }
        }
        stage('Remove old jar from DOCKER') {
            steps {
                script {
                    sh "sudo rm DOCKER/spring-boot-with-prometheus-0.1.0.jar"
                }
            }
        }
        stage('copy jar to DOCKER') {
            steps {
                script {
                    sh "sudo cp build/libs/spring-boot-with-prometheus-0.1.0.jar DOCKER/"
                }
            }
        }
        stage('building docker image') {
            steps {
                script {
                    sh "cd DOCKER; sudo docker build -t java-poc:v_${BUILD_NUMBER} ."
                }
            }
        }
        // stage('dcoker push') {
        //     steps {
        //         script {
        //             sh "sudo docker push harshvardhan1402/microk8s-kubernetes-poc:v_${BUILD_NUMBER}"
        //         }
        //     }
        // }
        stage('deploying on microk8s') {
            steps {
                script {
                    sh '''
                        #docker rmi -f java-poc
                        docker run -it -d -p 8282:8080 java-poc:v_${BUILD_NUMBER}
                        docker ps

                    '''
                }
            }
        }
        stage ('remove images from build server') {
            steps {
                script {
                    try {
                        sh "sudo docker images -a -q > images_cid; sudo docker rmi `cat images_cid`"
                    } catch (err) {
                        echo err.getMessage()
                    }
                }
                echo currentBuild.result
            }
        }
        // stage('remove unused images') {
        //     steps {
        //         script {
        //             try {
        //                 sh "sudo docker rmi `cat unused_images_cid`"
        //             } catch (err) {
        //                 echo err.getMessage()
        //             }
        //         }
        //         echo currentBuild.result
        //     }
        // }
    }
}
