def jenkins_path = " /var/lib/jenkins"
def tf_path      = "${jenkins_path}/terraform/build"
def ansible_path = "${jenkins_path}/ansible"
def terraform    = "/usr/local/bin/terraform"

def cgreen_name = ""

node {
    stage('scm'){
        checkout scm
    }

    stage('build & test'){
        sh "echo Building code and test.''"
    }

    stage('Confirm current green server'){
        //現在のGreenサーバの情報(AWS関係)を取得
        dir("${tf_path}"){
            option = "\$3"
            id = sh returnStdout: true, script: "${terraform} state show aws_lb_target_group_attachment.green_attach | grep target_id | awk '{print ${option}}'"
            try{
                result = sh returnStdout: true, script: "${terraform} state show aws_instance.2anet_server1 | grep ${id}"
                cgreen_name = "2anet_server1"
            }catch(exception){
                cgreen_name = "2anet_server2"
            }
        }
        sh "echo ${cgreen_name}"
    }

    stage('Destroy of the current green server'){
        //現在のGreenサーバを破棄
        dir("${tf_path}"){
            sh "${terraform} destroy -auto-approve -target=aws_instance.${cgreen_name} ./stage1"
        }  
    }

    stage('Create new blue server instance'){
        //新しいBlueサーバのインスタンスを作成
        //旧Greenサーバと同じtrageGroup
        dir("${tf_path}"){
            sh "${terraform} apply -auto-approve ./stage1"
        }
    }

    stage('Provisioning for new blue server'){
        dir("${tf_path}"){
            option = "\$3"
            id = sh returnStdout: true, script: "${terraform} state show aws_instance.${cgreen_name} | egrep '^public_ip' | awk '${option}}'"
        }
        sh "echo ${ip}"
        dir ("${ansible_path}"){

        }

        //Ansibleを使用して新しいBlueサーバを設定する
    }

    stage('Destroy of the current green server'){
        sh"echo 'server test'"
    }
    stage('Switch the new blue server'){
        //現Blueサーバと新BlueサーバのTargetGroupを切り替える
    }

}