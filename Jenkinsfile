node {
    stage ('소스체크아웃') {
        checkout([$class: 'GitSCM',
            branches: [[name: '*/master']],
            doGenerateSubmoduleConfigurations: false, extensions: [],
            submoduleCfg: [],
            userRemoteConfigs: [[credentialsId: 'DevPro', url: 'https://devpro.ktds.co.kr/msa/sidecar.git']]])
    }

    stage ('빌드') {
        sh './gradlew clean build'
    }

    stage ('스크립팅'){
        sh 'ssh ec2-user@ip-172-31-7-235 "mkdir -p /home/ec2-user/sidecar/log"'
        sh 'scp ./start.sh ec2-user@ip-172-31-7-235:/home/ec2-user/sidecar'
        sh 'scp ./shutdown.sh ec2-user@ip-172-31-7-235:/home/ec2-user/sidecar'
        sh 'ssh ec2-user@ip-172-31-7-235 "chmod a+x /home/ec2-user/sidecar/*.sh"'
    }

    stage ('서버 중지'){
        sh 'ssh ec2-user@ip-172-31-7-235 "/home/ec2-user/sidecar/shutdown.sh || true"'
    }

    stage ('배포') {
        sh 'scp build/libs/sidecar-1.0.0-RELEASE.jar ec2-user@ip-172-31-7-235:/home/ec2-user/sidecar'
    }

    stage ('서버 시작') {
        sh 'ec2-user@ip-172-31-7-235 "/home/ec2-user/sidecar/start.sh /home/ec2-user/sidecar/sidecar-1.0.0-RELEASE.jar prd"'
    }

}