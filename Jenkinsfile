properties([disableConcurrentBuilds()])
//def branch_name = "${params.BRANCH}" //название ветки, которое будем получать из гита после пул реквеста
def branch_name = "${branch}"
def low_branch_name = branch_name.toLowerCase() // нижний регистр в названии папки
def branch_name_db = low_branch_name.replaceAll('-','_');
//def status = '${state}' //название ветки, которое будем получать из гита после пул реквеста
//echo "${state}"
pipeline{
    agent{
        label 'master'
    }
    options{
        buildDiscarder(logRotator(numToKeepStr: '10', artifactNumToKeepStr: '5'))
       
    }
    stages{
        
        stage("Print variable"){
            when {expression { "${to_ref}" =="refs/heads/stage" } } 
            steps{
                echo "Автор коммита или пул реквеста ${who_author}"
               echo "Коментарий ${comment}"
               echo "ID коммита или пул реквеста ${comment_id}"
               echo "Текуща ветка ${branch}"
               echo "Ветка для слияния ${from_ref}"
               echo "Ветка ожидающая слияния ${to_ref}"
            }
        }
        
        stage ("Delete remote folder") {
            when {expression { "${to_ref}" =="refs/heads/stage" } } 
                steps{
                   sh "ssh sales@194.58.101.126 \'sudo rm -rf /var/www/html/'${low_branch_name}'-sales\'"
            }
        }
        stage("Drop database"){
            when {expression { "${to_ref}" =="refs/heads/stage" } } 
            steps{
               withCredentials([usernamePassword(credentialsId: 'stage_mysql',passwordVariable: 'pass', usernameVariable: 'username')]){
                   sh "ssh sales@194.58.101.126 \'mysql -u '${username}' -p'${pass}' -e \"drop database sales_${branch_name_db};\"\'"
                    }
            }
        }
        stage("Clean workspace"){
            when {expression { "${to_ref}" =="refs/heads/stage" } } 
            steps{
                cleanWs()
            }
        }
    }
}
properties([disableConcurrentBuilds()])
def branch_name = "stage"//название ветки, которое будем получать из гита после пул реквеста
def low_branch_name = branch_name.toLowerCase() // нижний регистр в названии папки
//def branch_name_db = low_branch_name.replaceAll('-','_'); 
//def status = '${state}' //название ветки, которое будем получать из гита после пул реквеста
//echo "${state}"
pipeline{
    agent{
        label 'master'
    }
    options{
        buildDiscarder(logRotator(numToKeepStr: '10', artifactNumToKeepStr: '5'))
       
    }
    stages{
        /*stage("Print variable"){
            when {expression { "${to_ref}" =="refs/heads/stage" } } 
            steps{
               echo "Автор коммита или пул реквеста ${who_author}"
               echo "Коментарий ${comment}"
               echo "ID коммита или пул реквеста ${comment_id}"
               echo "Текуща ветка ${branch}"
               echo "Ветка для слияния ${from_ref}"
               echo "Ветка ожидающая слияния ${to_ref}"
            }
        }
        stage ("Checkout current branch and Pull change from STAGE BRANCH"){ 
            when {expression { "${to_ref}" =="refs/heads/stage" } } 
            steps{
               sshagent (credentials: ['BitBucket']) {
                    sh ("git branch")
                    sh("git checkout ${branch} && ls -la && git pull ssh://git@bitbucket.lan.ubrr.ru:7999/devops/sales.git ${branch}")
                    sh ("git branch")
                    }
            }
        }
         stage ("Sync local folder and remote folder from STAGE BRANCH"){
                when {expression { "${to_ref}" =="refs/heads/stage" } } 
                steps {
                    sh("git checkout ${branch_name}")
                    sh "ssh sales@194.58.101.126 \'cd /var/www/sales_bitbucket\'"
                    sh  "rsync  -azvri   --recursive --include=.env.example  --delete --progress --verbose $WORKSPACE/ sales@194.58.101.126:/var/www/sales_bitbucket"
                }
        }
         stage("Rename .env.example to .env from STAGE BRANCH"){
           when {expression { "${to_ref}" =="refs/heads/stage" } } 
            steps{
                echo "=====================================Rename .env.example to .env====================="
                sh "ssh sales@194.58.101.126 \'cd /var/www/sales_bitbucket && mv .env.example .env\'"
            }
        }
        stage ("Run composer install from STAGE BRANCH"){
          when {expression { "${to_ref}" =="refs/heads/stage" } } 
            steps {
                echo "=====================================Run composer install====================="
                //sh 'composer install --no-ansi -n --no-progress --no-suggest'
                sh "ssh sales@194.58.101.126 \'cd /var/www/sales_bitbucket  && composer install --no-ansi -n --no-progress --no-suggest\'"
            }
        }*/
        
        /*stage("Clean workspace"){
            steps{
                cleanWs()
            }
        }*/
        stage ("Get folder from github"){
            steps{
                git branch: "stage", 
                //url: 'ssh://git@bitbucket.lan.ubrr.ru:7999/devops/sales.git',
                url: 'ssh://git@bitbucket.lan.ubrr.ru:7999/sal/sales.git',
                credentialsId: 'BitBucket'
            }
        }
        /*stage ("Checkout current branch and Pull change"){
           
            steps{
               sshagent (credentials: ['BitBucket']) {
                    sh ("git branch")
                    sh("git checkout ${branch_name} && ls -la && git pull ssh://git@bitbucket.lan.ubrr.ru:7999/devops/sales.git ${branch_name}")
                    sh ("git branch")
                    }
            }
        }*/
        /*stage ("Create remote folder") {
                steps{
                   sh "ssh sales@194.58.101.126 \'mkdir /var/www/html/'${low_branch_name}'-sales\'"
            }
        }*/
        stage ("Clear assets folder")
            {
                
                steps{
                   sh "ssh sales@194.58.101.126 \'rm -rf /var/www/sales/assets/*\'"
            }
        }
        stage ("Sync local folder and remote folder "){
           
                steps {
                    sh("git checkout ${branch_name}")
                    sh "ssh sales@194.58.101.126 \'cd /var/www/sales\'"
                    sh  "rsync  -azvri  --exclude \'*.env\' --exclude \'vendor/*\' --exclude \'uploads/*\' --exclude \'.git\' --recursive --include=.env.example  --delete --progress --verbose $WORKSPACE/ sales@194.58.101.126:/var/www/sales"
                }
        }
        stage ("Add permisions folder "){
                steps {
                    sh "ssh sales@194.58.101.126 \'chmod g+w /var/www/sales/assets\'"
                    sh "ssh sales@194.58.101.126 \'chmod g+w /var/www/sales/protected/runtime\'"
                }
        }
        
        /*stage("Rename .env.example to .env"){
           
            steps{
                echo "=====================================Rename .env.example to .env====================="
                sh "ssh sales@194.58.101.126 \'cd /var/www/sales_bitbucket && mv .env.example .env\'"
            }
        }*/
        stage ("Run composer install"){
          
            steps {
                echo "=====================================Run composer install====================="
                //sh 'composer install --no-ansi -n --no-progress --no-suggest'
                sh "ssh sales@194.58.101.126 \'cd /var/www/sales && composer install --no-ansi -n --no-progress --no-suggest\'"
            }
        }
         stage ("Get current build date  "){
                steps {
                    sh "ssh sales@194.58.101.126 \'date > /var/www/sales/build/index.html\'"
                   
                }
        }
        /*stage ("Copy upload folder")
            {
                steps{
                   sh "ssh sales@194.58.101.126 \'cp -R /var/www/sales_gitlab/uploads/ /var/www/sales\'"
            }
        }/*
        /*stage("Mysql create database"){
            steps{
               withCredentials([usernamePassword(credentialsId: 'stage_mysql',passwordVariable: 'pass', usernameVariable: 'username')]){
                   sh "ssh sales@194.58.101.126 \'mysql -u root -p'${pass}' -e \"create database sales_'${branch_name_db}';\"\'"
                    }
            }
        }
        stage("Mysql load dump in the new database"){
            steps{
               withCredentials([usernamePassword(credentialsId: 'stage_mysql',passwordVariable: 'pass', usernameVariable: 'username')]){
                   sh "ssh sales@194.58.101.126 \'mysql -u root -p'${pass}' sales_'${branch_name_db}' < /tmp/dump_sales.sql \'"
                    }
            }
        }
       stage("Change config stage.php"){
            steps{
                //echo "${branch_name_replace}"
                echo "=====================================Change right for folder====================="
                sh "ssh sales@194.58.101.126 sed -i \"s/\'mysql:host=localhost;dbname=sales\'/\'mysql:host=localhost;dbname=sales_'${branch_name_db}'\'/g\" /var/www/html/'${low_branch_name}'-sales/protected/config/stage.php"
            }
        }*/
        /*
        stage("Clean workspace"){
            steps{
                cleanWs()
            }
        }*/
    }
}

properties([disableConcurrentBuilds()])
def branch_name = "${branch}"//название ветки, которое будем получать из гита после пул реквеста
def low_branch_name = branch_name.toLowerCase() // нижний регистр в названии папки
def branch_name_db = low_branch_name.replaceAll('-','_'); 
//def status = '${state}' //название ветки, которое будем получать из гита после пул реквеста
//echo "${state}"
pipeline{
    agent{
        label 'master'
    }
    options{
        buildDiscarder(logRotator(numToKeepStr: '10', artifactNumToKeepStr: '5'))
       
    }
    stages{
        stage("Print variable"){
            //when {expression { "${to_ref}" =="refs/heads/stage" } } 
            steps{
               echo "Автор коммита или пул реквеста ${who_author}"
               echo "Коментарий ${comment}"
               echo "ID коммита или пул реквеста ${comment_id}"
               echo "Текуща ветка ${branch}"
               echo "Ветка для слияния ${from_ref}"
               echo "Ветка ожидающая слияния ${to_ref}"
            }
        }
       /*stage ("Checkout current branch and Pull change from STAGE BRANCH"){ 
            when {expression { "${to_ref}" =="refs/heads/stage" } } 
            steps{
               sshagent (credentials: ['BitBucket']) {
                    sh ("git branch")
                    sh("git checkout ${branch} && ls -la && git pull ssh://git@bitbucket.lan.ubrr.ru:7999/devops/sales.git ${branch}")
                    sh ("git branch")
                    }
            }
        }
         stage ("Sync local folder and remote folder from STAGE BRANCH"){
                when {expression { "${to_ref}" =="refs/heads/stage" } } 
                steps {
                    sh("git checkout ${branch_name}")
                    sh "ssh sales@194.58.101.126 \'cd /var/www/sales_bitbucket\'"
                    sh  "rsync  -azvri   --recursive --include=.env.example  --delete --progress --verbose $WORKSPACE/ sales@194.58.101.126:/var/www/sales_bitbucket"
                }
        }
         stage("Rename .env.example to .env from STAGE BRANCH"){
           when {expression { "${to_ref}" =="refs/heads/stage" } } 
            steps{
                echo "=====================================Rename .env.example to .env====================="
                sh "ssh sales@194.58.101.126 \'cd /var/www/sales_bitbucket && mv .env.example .env\'"
            }
        }
        stage ("Run composer install from STAGE BRANCH"){
          when {expression { "${to_ref}" =="refs/heads/stage" } } 
            steps {
                echo "=====================================Run composer install====================="
                //sh 'composer install --no-ansi -n --no-progress --no-suggest'
                sh "ssh sales@194.58.101.126 \'cd /var/www/sales_bitbucket  && composer install --no-ansi -n --no-progress --no-suggest\'"
            }
        }*/
        
        /*stage("Clean workspace"){
            steps{
                cleanWs()
            }
        }*/
        stage ("Get folder from github"){
            //when {expression { "${to_ref}" =="refs/heads/stage" } }
            steps{
                git branch: "master", 
                url: 'ssh://git@bitbucket.lan.ubrr.ru:7999/devops/sales.git',
                credentialsId: 'BitBucket'
            }
        }
        stage ("Checkout current branch and Pull change"){
           //when {expression { "${to_ref}" =="refs/heads/stage" } }
            steps{
               sshagent (credentials: ['BitBucket']) {
                    sh ("git branch")
                    sh("git checkout ${branch_name} && ls -la && git pull ssh://git@bitbucket.lan.ubrr.ru:7999/devops/sales.git ${branch_name}")
                    sh ("git branch")
                    }
            }
        }
        stage ("Create remote folder") {
            //when {expression { "${to_ref}" =="refs/heads/stage" } }
                steps{
                   sh "ssh sales@194.58.101.126 \'mkdir /var/www/html/'${low_branch_name}'-sales\'"
            }
        }
        stage ("Copy template folder")
            {
               // when {expression { "${to_ref}" =="refs/heads/stage" } }
                steps{
                   sh "ssh sales@194.58.101.126 \' cp -R /var/www/html/master-sales/. /var/www/html/'${low_branch_name}'-sales\'"
            }
        }
        stage ("Sync local folder and remote folder "){
           //when {expression { "${to_ref}" =="refs/heads/stage" } }
                steps {
                    sh("git checkout ${branch_name}")
                    sh "ssh sales@194.58.101.126 \'cd /var/www/html/'${low_branch_name}'-sales\'"
                    sh  "rsync  -azvri   --recursive --include=.env.example --exclude=\'.git\'  --delete --progress --verbose $WORKSPACE/ sales@194.58.101.126:/var/www/html/${low_branch_name}-sales"
                }
        }
        stage ("Add permisions folder "){
            //when {expression { "${to_ref}" =="refs/heads/stage" } }
                steps {
                    sh "ssh sales@194.58.101.126 \'chmod g+w /var/www/html/'${low_branch_name}'-sales/assets\'"
                    sh "ssh sales@194.58.101.126 \'chmod g+w /var/www/html/'${low_branch_name}'-sales/protected/runtime\'"
                }
        }
        
        stage("Rename .env.example to .env"){
            //when {expression { "${to_ref}" =="refs/heads/stage" } }
            steps{
                echo "=====================================Rename .env.example to .env====================="
                sh "ssh sales@194.58.101.126 \'cd /var/www/html/'${low_branch_name}'-sales && mv .env.example .env\'"
            }
        }
        stage ("Run composer install"){
           //when {expression { "${to_ref}" =="refs/heads/stage" } }
            steps {
                echo "=====================================Run composer install====================="
                //sh 'composer install --no-ansi -n --no-progress --no-suggest'
                sh "ssh sales@194.58.101.126 \'cd /var/www/html/'${low_branch_name}'-sales && composer install --no-ansi -n --no-progress --no-suggest\'"
            }
        }
        stage ("Copy upload folder")
            {
                //when {expression { "${to_ref}" =="refs/heads/stage" } }
                steps{
                   sh "ssh sales@194.58.101.126 \'cp -R /var/www/sales/uploads/ /var/www/html/'${low_branch_name}'-sales/uploads\'"
            }
        }
        stage("Mysql create database"){
            //when {expression { "${to_ref}" =="refs/heads/stage" } }
            steps{
               withCredentials([usernamePassword(credentialsId: 'stage_mysql',passwordVariable: 'pass', usernameVariable: 'username')]){
                   sh "ssh sales@194.58.101.126 \'mysql -u root -p'${pass}' -e \"create database sales_'${branch_name_db}';\"\'"
                    }
            }
        }
        stage("Mysql load dump in the new database"){
            //when {expression { "${to_ref}" =="refs/heads/stage" } }
            steps{
               withCredentials([usernamePassword(credentialsId: 'stage_mysql',passwordVariable: 'pass', usernameVariable: 'username')]){
                   sh "ssh sales@194.58.101.126 \'mysql -u root -p'${pass}' sales_'${branch_name_db}' < /tmp/dump_sales.sql \'"
                    }
            }
        }
       stage("Change config in development.php"){
           //when {expression { "${to_ref}" =="refs/heads/stage" } }
            steps{
                //echo "${branch_name_replace}"
                echo "=====================================Change right for folder====================="
                sh "ssh sales@194.58.101.126 sed -i \"s/\'mysql:host=localhost;dbname=sales\'/\'mysql:host=localhost;dbname=sales_'${branch_name_db}'\'/g\" /var/www/html/'${low_branch_name}'-sales/protected/config/development.php"
            }
        }
        /*
        stage("Clean workspace"){
            steps{
                cleanWs()
            }
        }*/
    }
    post {
    //when {expression { "${to_ref}" =="refs/heads/stage" } }
        success {
            echo "${env.BUILD_URL} has result success"
            emailext body: "Сборка ${env.BUILD_URL} выполнилась\n\nАвтор коммита или пул реквеста ${who_author}\nКоментарий ${comment}\nID коммита или пул реквеста ${comment_id}\nТекуща ветка ${branch}\nВетка для слияния ${from_ref}\nВетка ожидающая слияния ${to_ref}\nСсылка на окружение https://${low_branch_name}-sales.ubrrdev.ru", subject: 'Сборка выолнилась успешно', to: 'chuvakov@ubrr.ru'
         }
        failure {
            echo "${env.BUILD_URL} has result fail"
            emailext body: "Сборка ${env.BUILD_URL} не выполнилась\nСмотри в логи", subject: 'Сборка не выполнилась', to: 'chuvakov@ubrr.ru'
        }
    }
}

properties([disableConcurrentBuilds()])
//def branch_name = '${branch}' //название ветки, которое будем получать из гита после пул реквеста
//def status = '${state}' //название ветки, которое будем получать из гита после пул реквеста
//echo "${state}"
def branch_name = "${branch}"
def low_branch_name = branch_name.toLowerCase() // нижний регистр в названии папки
def branch_name_db = low_branch_name.replaceAll('-','_');

pipeline{
    agent{
        label 'master'
    }
    options{
        buildDiscarder(logRotator(numToKeepStr: '10', artifactNumToKeepStr: '5'))
       
    }
    stages{
        /*stage ("Print variable"){
            steps {
                echo branch_name
            }
        }*/
        stage("Print variable"){
            when {expression { "${to_ref}" =="refs/heads/stage" } } 
            steps{
               echo "Автор коммита или пул реквеста ${who_author}"
               echo "Коментарий ${comment}"
               echo "ID коммита или пул реквеста ${comment_id}"
               echo "Текуща ветка ${branch}"
               echo "Ветка для слияния ${from_ref}"
               echo "Ветка ожидающая слияния ${to_ref}"
            }
        }
        
        stage("Clean workspace"){
            when {expression { "${to_ref}" =="refs/heads/stage" } }
            steps{
                cleanWs()
            }
        }
        stage ("Sync local folder and remote folder "){
           when {expression { "${to_ref}" =="refs/heads/stage" } }
                steps {
                    ws("/var/lib/jenkins/workspace/DevOpsTest/SALES_BITBUCKET/sales_open/"){
                        //sh("git checkout ${branch_name}")
                    sshagent (credentials: ['BitBucket']) {
                        sh("git checkout ${branch_name} && ls -la && git pull ssh://git@bitbucket.lan.ubrr.ru:7999/devops/sales.git ${branch_name}")
                        sh "ssh sales@194.58.101.126 \'cd /var/www/html/'${low_branch_name}'-sales\'"
                        sh  "rsync  -azvri   --recursive --include=.env.example --exclude=\'.git\' --exclude \'*.env\' --exclude \'vendor/*\'  --exclude \'protected/config/*\' --delete --progress --verbose /var/lib/jenkins/workspace/DevOpsTest/SALES_BITBUCKET/sales_open/ sales@194.58.101.126:/var/www/html/${low_branch_name}-sales"
                    }
                }
            }
        }
         stage ("Add permisions folder "){
           when {expression { "${to_ref}" =="refs/heads/stage" } }
                steps {
                    sh "ssh sales@194.58.101.126 \'chmod g+w /var/www/html/'${low_branch_name}'-sales/assets\'"
                    sh "ssh sales@194.58.101.126 \'chmod g+w /var/www/html/'${low_branch_name}'-sales/protected/runtime\'"
                }
        }
        
        /*stage("Rename .env.example to .env"){
           
            steps{
                echo "=====================================Rename .env.example to .env====================="
                sh "ssh sales@194.58.101.126 \'cd /var/www/html/'${low_branch_name}'-sales && mv .env.example .env\'"
            }
        }
        stage ("Run composer install"){
            steps {
                echo "=====================================Run composer install====================="
                //sh 'composer install --no-ansi -n --no-progress --no-suggest'
                sh "ssh sales@194.58.101.126 \'cd /var/www/html/'${low_branch_name}'-sales && composer install --no-ansi -n --no-progress --no-suggest\'"
            }
        }*/
        stage ("Copy upload folder")
            {
                when {expression { "${to_ref}" =="refs/heads/stage" } }
                steps{
                   sh "ssh sales@194.58.101.126 \'cp -R /var/www/sales/uploads/ /var/www/html/'${low_branch_name}'-sales/uploads\'"
            }
        }
        /*stage("Change config stage.php"){
            steps{
                //echo "${branch_name_replace}"
                echo "=====================================Change right for folder====================="
                sh "ssh sales@194.58.101.126 sed -i \"s/\'mysql:host=localhost;dbname=sales\'/\'mysql:host=localhost;dbname=sales_'${branch_name_db}'\'/g\" /var/www/html/'${low_branch_name}'-sales/protected/config/stage.php"
            }
        }*/
    }
}