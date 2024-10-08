@Library('slack') _

/////// ******************************* Code for fectching Failed Stage Name ******************************* ///////
import io.jenkins.blueocean.rest.impl.pipeline.PipelineNodeGraphVisitor
import io.jenkins.blueocean.rest.impl.pipeline.FlowNodeWrapper
import org.jenkinsci.plugins.workflow.support.steps.build.RunWrapper
import org.jenkinsci.plugins.workflow.actions.ErrorAction

// Get information about all stages, including the failure cases
// Returns a list of maps: [[id, failedStageName, result, errors]]
@NonCPS
List<Map> getStageResults(RunWrapper build) {
  // Get all pipeline nodes that represent stages
  def visitor = new PipelineNodeGraphVisitor(build.rawBuild)
  def stages = visitor.pipelineNodes.findAll { it.type == FlowNodeWrapper.NodeType.STAGE }

  return stages.collect { stage ->
        // Get all the errors from the stage
        def errorActions = stage.getPipelineActions(ErrorAction)
        def errors = errorActions?.collect { it.error }.unique()

        return [
            id: stage.id,
            failedStageName: stage.displayName,
            result: "${stage.status.result}",
            errors: errors
        ]
  }
}
// Get information of all failed stages not ok
@NonCPS
List<Map> getFailedStages(RunWrapper build) {
  return getStageResults(build).findAll { it.result == 'FAILURE' }
}

/////// ******************************* Code for fectching Failed Stage Name ******************************* ///////

pipeline {
  agent any

  environment {
    deploymentName = 'devsecops'
    containerName = 'devsecops-container'
    serviceName = 'devsecops-svc'
    imageName = "hrefnhaila/numeric-app:${GIT_COMMIT}"
    applicationURL = 'http://devsecopstp1.eastus.cloudapp.azure.com'
    applicationURI = '/increment/99'
    GIT_PREVIOUS_SUCCESSFUL_COMMIT = sh(script: 'git rev-parse HEAD~1', returnStdout: true).trim()
  }

  stages {
    // stage('Checkout') {
    //   steps {
    //     // Check out the repository
    //     checkout scm

    //     // Manually capture the GIT_COMMIT hash
    //     script {
    //       env.GIT_COMMIT = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
    //       echo "Git commit hash: ${env.GIT_COMMIT}"
    //       // Get the branch name
    //       env.GIT_BRANCH = sh(returnStdout: true, script: 'git rev-parse --abbrev-ref HEAD').trim()
    //       echo "Git branch: ${env.GIT_BRANCH}"
    //     }
    //   }
    // }
    // stage('Build Artifact - Maven') {
    //   steps {
    //     sh 'mvn clean package -DskipTests=true'
    //     archive 'target/*.jar'
    //   }
    // }

    // stage('Unit Tests - JUnit and JaCoCo') {
    //   steps {
    //     sh 'mvn test'
    //   }
    // }

    // stage('Mutation Tests - PIT') {
    //   steps {
    //     sh 'mvn org.pitest:pitest-maven:mutationCoverage'
    //   }
    // }

    // // stage('SonarQube - SAST') {
    // //   steps {
    // //     withSonarQubeEnv('SonarQube') {
    // //       sh "mvn sonar:sonar \
    // //                   -Dsonar.projectKey=numeric-application \
    // //                   -Dsonar.host.url=http://devsecopstp1.eastus.cloudapp.azure.com:9000"
    // //     }
    // //     timeout(time: 2, unit: 'MINUTES') {
    // //       script {
    // //         waitForQualityGate abortPipeline: true
    // //       }
    // //     }
    // //   }
    // // }

    // stage('Vulnerability Scan - Docker') {
    //   steps {
    //     parallel(
    //         'Dependency Scan': {
    //            catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
    //             sh 'mvn dependency-check:check'
    //            }
    //         },
    //         'Trivy Scan': {
    //            catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
    //             sh 'sudo bash trivy-docker-image-scan.sh'
    //            }
    //         },
    //         'OPA Conftest': {
    //            catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
    //             sh 'sudo docker run --rm -v $(pwd):/project openpolicyagent/conftest test --policy opa-docker-security.rego Dockerfile'
    //            }
    //         }
    //       )
    //   }
    // }

    // stage('Docker Build and Push') {
    //   steps {
    //     catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
    //       withDockerRegistry([credentialsId: 'docker-hub', url: '']) {
    //         sh 'printenv'
    //         sh 'sudo docker build -t hrefnhaila/numeric-app:""$GIT_COMMIT"" .'
    //         sh 'sudo docker push hrefnhaila/numeric-app:""$GIT_COMMIT""'
    //       }
    //     }
    //   }
    // }

    // stage('Vulnerability Scan - Kubernetes') {
    //   steps {
    //     parallel(
    //       'OPA Scan': {
    //          catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
    //         sh 'sudo docker run --rm -v $(pwd):/project openpolicyagent/conftest test --policy opa-k8s-security.rego k8s_deployment_service.yaml'
    //          }
    //       },
    //       'Kubesec Scan': {
    //          catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
    //         sh 'sudo bash kubesec-scan.sh'
    //          }
    //       },
    //       'Trivy Scan': {
    //          catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
    //         sh 'sudo bash trivy-k8s-scan.sh'
    //          }
    //       }
    //     )
    //   }
    // }

    // stage('K8S Deployment - DEV') {
    //   steps {
    //     parallel(
    //       'Deployment': {
    //         withKubeConfig([credentialsId: 'kubeconfig']) {
    //            catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
    //           sh 'sudo bash k8s-deployment.sh'
    //            }
    //         }
    //       },
    //       'Rollout Status': {
    //         withKubeConfig([credentialsId: 'kubeconfig']) {
    //            catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
    //           sh 'sudo bash k8s-deployment-rollout-status.sh'
    //            }
    //         }
    //       }
    //     )
    //   }
    // }

    // stage('Integration Tests - DEV') {
    //   steps {
    //     script {
    //       try {
    //         withKubeConfig([credentialsId: 'kubeconfig']) {
    //           catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
    //             sh 'sudo bash integration-test.sh'
    //           }
    //         }
    //       } catch (e) {
    //         withKubeConfig([credentialsId: 'kubeconfig']) {
    //           catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
    //             sh "kubectl -n default rollout undo deploy ${deploymentName}"
    //           }
    //         }
    //         throw e
    //       }
    //     }
    //   }
    // }

    // stage('OWASP ZAP - DAST') {
    //   steps {
    //     withKubeConfig([credentialsId: 'kubeconfig']) {
    //       catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
    //         sh 'sudo bash zap.sh'
    //       }
    //     }
    //   }
    // }

    // stage('Prompte to PROD?') {
    //   steps {
    //     timeout(time: 2, unit: 'DAYS') {
    //       input 'Do you want to Approve the Deployment to Production Environment/Namespace?'
    //     }
    //   }
    // }

    // stage('K8S CIS Benchmark') {
    //   steps {
    //     script {
    //       parallel(
    //         'Master': {
    //           sh 'sudo bash cis-master.sh'
    //         },
    //         'Etcd': {
    //           sh 'sudo bash cis-etcd.sh'
    //         },
    //         'Kubelet': {
    //           sh 'sudo bash cis-kubelet.sh'
    //         }
    //       )
    //     }
    //   }
    // }

    // stage('K8S Deployment - PROD') {
    //   steps {
    //     parallel(
    //       'Deployment': {
    //         withKubeConfig([credentialsId: 'kubeconfig']) {
    //            catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
    //           sh "sed -i 's#replace#${imageName}#g' k8s_PROD-deployment_service.yaml"
    //           sh 'kubectl -n prod apply -f k8s_PROD-deployment_service.yaml'
    //            }
    //         }
    //       },
    //       'Rollout Status': {
    //         withKubeConfig([credentialsId: 'kubeconfig']) {
    //            catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
    //           sh 'sudo bash k8s-PROD-deployment-rollout-status.sh'
    //            }
    //         }
    //       }
    //     )
    //   }
    // }

    // stage('Integration Tests - PROD') {
    //   steps {
    //     script {
    //       try {
    //         withKubeConfig([credentialsId: 'kubeconfig']) {
    //           catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
    //             sh 'sudo bash integration-test-PROD.sh'
    //           }
    //         }
    //       } catch (e) {
    //         withKubeConfig([credentialsId: 'kubeconfig']) {
    //           catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
    //             sh "kubectl -n prod rollout undo deploy ${deploymentName}"
    //           }
    //         }
    //         throw e
    //       }
    //     }
    //   }
    // }

    stage('Build') {
      steps {
        script {
          sendNotification('STARTED')
        }
        sh 'exit 0'
      }
    }

        stage('Test') {
      steps {
        sh 'exit 0'
      }
        }

        stage('Deploy') {
      steps {
        sh 'exit 1'
      }
        }
  }

  post {
        always {
          // junit 'target/surefire-reports/*.xml'
          // jacoco execPattern: 'target/jacoco.exec'
          // pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'
          // dependencyCheckPublisher pattern: 'target/dependency-check-report.xml'
          // publishHTML([allowMissing: false, alwaysLinkToLastBuild: true, keepAll: true, reportDir: 'owasp-zap-report', reportFiles: 'zap_report.html', reportName: 'OWASP ZAP HTML Report', reportTitles: 'OWASP ZAP HTML Report'])

        //Use sendNotifications.groovy from shared library and provide current build result as parameter
        // sendNotification currentBuild.result
        }

    success {
      script {
        sendNotification('SUCCESS')
      }
    }
        failure {
      script {
        sendNotification('FAILURE')
      }
        }
        unstable {
      script {
        sendNotification('UNSTABLE')
      }
        }
  }
}
