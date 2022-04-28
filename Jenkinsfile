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
                                name: 'NUMNODOSCLUSTER#KS',
                                trim: true
                            ),
                            string(
                                defaultValue: 'CTT-EKS-CLUSTER',
                                name: 'NAMECLUSTER',
                                trim: true
                            ),
                            string(
                                defaultValue: 't3.medium',
                                name: 'SIZEMACHINE',
                                trim: true
                            )
                        ])
                    ])

                  echo "Hello World! ${AWS_SECRET_KEY}"
                  sh 'aws --profile default configure set aws_access_key_id ${AWS_SECRET_KEY}'
                }
            }
        }
    }
}
