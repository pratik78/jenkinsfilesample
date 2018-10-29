pipeline {
  agent none
  stages {
    stage('Kickoff') {
      steps {
        sh '''
#!/bin/bash -ex

set -x

git clone ${environmentsUrl} ${WORKSPACE}/environments
git clone ${adfdeployUrl} ${WORKSPACE}/adfdeploy
git clone ${operationsUrl} ${WORKSPACE}/operations

mkdir ${WORKSPACE}/maven
cp ${mavenSettingsFile} ${WORKSPACE}/maven
cp ${mavenSettingsSecurityFile} ${WORKSPACE}/maven

# for ansible encrypted files
echo ${ansible_vault_pass1} > ${WORKSPACE}/ansible_vault_pass.txt

prepareEnv() {

mkdir ${WORKSPACE}/prepare_${1}

eval sshKeyDir="\\${${1}SshKey}"
cp $sshKeyDir ${WORKSPACE}/prepare_${1}/azure.key

sshKey=/workspace/prepare_${1}/azure.key
workspace_name=`echo ${JOB_NAME} | grep -Po \'^[0-9a-zA-Z_]+(?=/)\'`
folder_layer=`echo ${JOB_NAME} | grep -Po "(?<=${workspace_name})[0-9a-zA-Z_/]+(?=${JOB_BASE_NAME})"`
vars_relative_dir=${workspace_name}_${1}${folder_layer}
group_name=${1}_${application}_${serverType}
vars_file_name=/workspace/environments/${vars_relative_dir}${application}_${serverType}_vars.yml
vault_file_name=/workspace/environments/${vars_relative_dir}${application}_${serverType}_vault.yml

#Prepare build properties file
cat <<EOF > ${WORKSPACE}/prepare_${1}/build.properties
sshUser=$sshUser
sshKey=$sshKey
vaultPass=$vaultPass
inventory=$inventory
workspace_name=$workspace_name
folder_layer=$folder_layer
vars_relative_dir=$vars_relative_dir
group_name=$group_name
vars_file_name=$vars_file_name
vault_file_name=$vault_file_name
EOF

#Prepare azure ini file
cat <<EOF > ${WORKSPACE}/prepare_${1}/azure_rm.ini
#
# Configuration file for azure_rm.py
#
[azure]
# Control which resource groups are included. By default all resources groups are included.
# Set resource_groups to a comma separated list of resource groups names.
resource_groups=${workspace_name}_${1}
# Control which tags are included. Set tags to a comma separated list of keys or key:value pairs
#tags=
# Control which locations are included. Set locations to a comma separated list (e.g. eastus,eastus2,westus)
#locations=
# Include powerstate. If you don\'t need powerstate information, turning it off improves runtime performance.
include_powerstate=no
# Control grouping with the following boolean flags. Valid values: yes, no, true, false, True, False, 0, 1.
group_by_resource_group=yes
group_by_location=no
group_by_security_group=no
group_by_tag=yes
EOF
}

echo "Prepare CI2 environment files!"
prepareEnv ci3 CI3

echo "Prepare TST2 environment files!"
prepareEnv tst2 TST2

echo "Prepare DEV22 environment files!"
prepareEnv dev2 DEV2

echo "Prepare trn2 environment files!"
prepareEnv trn2 trn2

echo "Prepare autotest environment files!"
prepareEnv autotest autotest


docker pull luismsousa/jdeveloper:12.2.1.0.0_jdk8u131

set +x
'''
        echo 'hello'
      }
    }
    stage('Build') {
      steps {
        sh 'echo "this is build job"'
      }
    }
  }
  environment {
    env = 'dev'
  }
}