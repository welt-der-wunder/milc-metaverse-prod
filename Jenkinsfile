node {


    properties([
        parameters (
              [
                booleanParam(defaultValue: false, description: 'Skip the frontendPart', name: 'skipFrontend'),
                booleanParam(defaultValue: false, description: 'Skip the server part', name: 'skipBackend')
              ]
            )]
         )

        //    stage('backup existing stage') {
        //        steps {
        //            withAWS(credentials:'aws-milc-production') {
        //                script {
        //                    def key = new Date().format("yyyyMMdd-HHmmss", TimeZone.getTimeZone('UTC'))
        //                        def files = s3FindFiles(bucket:'stageci.metaverse.milc.global', glob:'**/*', onlyFiles: true) + s3FindFiles(bucket:'stageci.metaverse.milc.global', glob:'*', onlyFiles: true)
        //                        files.each {
        //                            s3Copy(fromBucket:'stageci.metaverse.milc.global',fromPath: it.path, toBucket:'metaverse-backups', toPath: "${key}_stage/${it.path}", acl:'PublicRead')
        //                                println it.path
        //                        }
        //                }
        //            }
        //        }
        //    }

        //    stage('deploy metaverse frontend test to prod') {
        //        steps {
        //            withAWS(credentials:'aws-milc-production') {
        //                s3Delete(bucket:'stageci.metaverse.milc.global', path:'/')
        //                    script {
        //                        def files = s3FindFiles(bucket:'testci.metaverse.milc.global', glob:'**/*', onlyFiles: true) + s3FindFiles(bucket:'testci.metaverse.milc.global', glob:'*', onlyFiles: true)
        //                            files.each {
        //                                s3Copy(fromBucket:'testci.metaverse.milc.global',fromPath: it.path, toBucket:'stageci.metaverse.milc.global', toPath: it.path, acl:'PublicRead')
        //                                    println it.path
        //                            }
        //                    }
        //            }
        //        }
        //    }

            stage('promote k8s backend from stage to prod') {
                if (params.skipBackend) {
                    sh 'echo "Skip kubernetetes deployment"'
                        return
                }
                docker.withRegistry("https://624622131033.dkr.ecr.eu-central-1.amazonaws.com", "ecr:eu-central-1:aws-ecr-credentials") {
                    docker.image('624622131033.dkr.ecr.eu-central-1.amazonaws.com/aws-cli:2.9.20-3').inside {
                        withAWS(credentials:'aws-milc-production') {
                            script {
                                sh "aws eks update-kubeconfig --name milc"
                                    ["ArtRoomNEW","AwardsLobby", "BuisnessIsland","LLShop","MCShop","OldenburgTown","OldenburgOpera"].collect {
                                        it.toLowerCase()
                                    }.each {
                                       def metaverseServerImageStage = sh(script: "kubectl get statefulset metaverseserverstage${it}  -o=jsonpath='{.spec.template.spec.containers[?(@.name==\"metaverseserver\")].image}'", returnStdout: true).trim() 
                                        println metaverseServerImageStage
                                        sh "kubectl set image sts/metaverseserverprod${it} metaverseserver=${metaverseServerImageStage}"
                                    }

                            }
                        }
                    }
                }

}



//    post {
//        success {
//            office365ConnectorSend webhookUrl: 'https://weltderwunder.webhook.office.com/webhookb2/15dede43-9a8f-4e9c-a8c4-d12b2835beee@4248087a-1b05-4c89-bc27-57c6f0ae5704/JenkinsCI/a8bc7be91ae54b41b21c9804241882e3/c8843c69-713e-4505-95ad-a1cedcdd3758',
//                                   message: 'Metaverse has been deployed to [staging](https://stageci.metaverse.milc.global/)',
//                                   status: 'Deployment Success',
//                                   color: '#00FF00'
//
//        }
//        failure {
//            office365ConnectorSend webhookUrl: 'https://weltderwunder.webhook.office.com/webhookb2/15dede43-9a8f-4e9c-a8c4-d12b2835beee@4248087a-1b05-4c89-bc27-57c6f0ae5704/JenkinsCI/a8bc7be91ae54b41b21c9804241882e3/c8843c69-713e-4505-95ad-a1cedcdd3758',
//                                   message: 'Error deploying Metaverse to staging',
//                                   status: 'Deployment Failed',
//                                   color: '#FF0000'
//        }
//    }

}
