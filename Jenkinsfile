pipeline {
    agent { label 'slave1' }
    stages {
        stage('configaws') {
            steps {
                script {
                    properties([
                        parameters([
                            string(
                                defaultValue: 'XYZXYZXYZXYZXYZXYZ',
                                name: 'AWS_ACCESS_KEY',
                                trim: true
                            ),
                            string(
                                defaultValue: 'ABCABCABCABCABC',
                                name: 'AWS_SECRET_KEY',
                                trim: true
                            ),
                            string(
                                defaultValue: 'us-east-2',
                                name: 'REGION',
                                trim: true
                            ),
                            string(
                                defaultValue: '2',
                                name: 'NUMNODOSCLUSTEREKS',
                                trim: true
                            ),
                            string(
                                defaultValue: 'CTT-EKS-CLUSTER',
                                name: 'NAMECLUSTER',
                                trim: true
                            ),
                            string(
                                defaultValue: 'nodes',
                                name: 'NODESGROUPNAME',
                                trim: true
                            ),
                            string(
                                defaultValue: 't3.medium',
                                name: 'SIZEMACHINE',
                                trim: true
                            ),
                            string(
                                defaultValue: '1.22',
                                name: 'K8SVERSION',
                                trim: true
                            )
                        ])
                    ])
                  sh 'aws --profile default configure set aws_access_key_id ${AWS_ACCESS_KEY}'
                  sh 'aws --profile default configure set aws_secret_access_key ${AWS_SECRET_KEY}'
                    
                }
            }
        }
        stage('createeks') { 
            steps { 
                sh 'echo ${NUMNODOSCLUSTEREKS}'
                sh 'eksctl create cluster --name ${NAMECLUSTER} --version ${K8SVERSION} --region ${REGION} --nodegroup-name ${NODESGROUPNAME} --node-type ${SIZEMACHINE} --nodes ${NUMNODOSCLUSTEREKS}'
            }
        }
        stage('Download') {
            steps {
                sh 'cat ~/.kube/config > kubeconfig'
            }
        }
        stage('deploy-ingress-nginx') { 
            steps { 
                sh 'helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx'
                sh 'helm install ingress-nginx ingress-nginx/ingress-nginx -n ingress-nginx'
            }
        }
        stage('deployjenkins') { 
            steps { 
                sh 'kubectl apply -f https://raw.githubusercontent.com/arrsvjes/ctt-devops-eks/main/jenkins.yaml'
            }
        }
    }
    post {
        always {
            archiveArtifacts artifacts: 'kubeconfig', onlyIfSuccessful: true
        }
    }
}
