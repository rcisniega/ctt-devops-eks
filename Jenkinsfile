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
                sh 'kubectl create namespace ingress-nginx'
                sh 'kubectl create namespace ingress-nginx-nexus'
                sh 'kubectl create namespace ingress-nginx-gitlab'
                sh 'helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx'
                sh 'helm install ingress-nginx ingress-nginx/ingress-nginx -n ingress-nginx'
                sh 'helm install ingress2 ingress-nginx/ingress-nginx --namespace ingress-nginx-nexus --set controller.ingressClassResource.name=inexus'
                sh 'helm install ingress3 ingress-nginx/ingress-nginx --namespace ingress-nginx-gitlab --set controller.ingressClassResource.name=igitlab'
            }
        }
        stage('deployjenkins') { 
            steps { 
                sh 'kubectl apply -f https://raw.githubusercontent.com/arrsvjes/ctt-devops-eks/main/jenkins.yaml'
            }
        }
        stage('deploynexus') { 
            steps { 
                sh 'kubectl apply -f https://raw.githubusercontent.com/arrsvjes/ctt-devops-eks/main/nexus-repository.yaml'
            }
        }
        stage('deploygitlab') { 
            steps { 
                sh 'kubectl apply -f https://raw.githubusercontent.com/arrsvjes/ctt-devops-eks/main/gitlab.yaml'
            }
        }
        stage('configureingress') { 
            steps {
                sh 'sleep 10'
                sh 'echo stage configureingress'
                sh 'kubectl apply -f https://raw.githubusercontent.com/arrsvjes/ctt-devops-eks/main/ingresses-jenkins.yaml'
                sh 'kubectl apply -f https://raw.githubusercontent.com/arrsvjes/ctt-devops-eks/main/ingresses-nexus.yaml'
                sh 'kubectl apply -f https://raw.githubusercontent.com/arrsvjes/ctt-devops-eks/main/ingresses-gitlab.yaml'
                sh 'wget https://raw.githubusercontent.com/arrsvjes/ctt-devops-eks/main/ingress-nginx-nexus-patch.yaml && kubectl patch deployment/ingress2-ingress-nginx-controller -n ingress-nginx-nexus --patch-file ingress-nginx-nexus-patch.yaml'
                sh 'wget https://raw.githubusercontent.com/arrsvjes/ctt-devops-eks/main/ingress-nginx-gitlab-patch.yaml && kubectl patch deployment/ingress3-ingress-nginx-controller -n ingress-nginx-gitlab --patch-file ingress-nginx-gitlab-patch.yaml'
            }
        }
    }
    post {
        always {
            sh 'sleep 30 && echo "Pausa de 30 seg, esperando se complete rollout"'
            archiveArtifacts artifacts: 'kubeconfig', onlyIfSuccessful: true
            sh 'kubectl get ingress -n jenkins > URL-Devops.txt'
            archiveArtifacts artifacts: 'URL-Devops.txt', onlyIfSuccessful: true
        }
    }
}
