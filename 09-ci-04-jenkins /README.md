# Домашнее задание к занятию 10 «Jenkins»
## Подготовка к выполнению
1. Создать два VM: для jenkins-master и jenkins-agent.
2. Установить Jenkins при помощи playbook.
3. Запустить и проверить работоспособность.
4. Сделать первоначальную настройку.
```
vagrant@server1:~/terraform/09_04_Jenkins/infrastructure# ansible-playbook -i inventory/cicd/hosts.yml site.yml

PLAY [Preapre all hosts] *****************************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************************
The authenticity of host '51.250.6.112 (51.250.6.122)' can't be established.
ECDSA key fingerprint is SHA256:q2On8wq0orKw+hr1fZT+cdIZKN1Fx+aE62TIQmqr9lM.
Are you sure you want to continue connecting (yes/no/[fingerprint])? ok: [jenkins-agent-01]
yes
ok: [jenkins-master-01]

TASK [Create group] **********************************************************************************************************************
changed: [jenkins-agent-01]
changed: [jenkins-master-01]

TASK [Create user] ***********************************************************************************************************************
changed: [jenkins-agent-01]
changed: [jenkins-master-01]

TASK [Install JDK] ***********************************************************************************************************************
changed: [jenkins-master-01]
changed: [jenkins-agent-01]

PLAY [Get Jenkins master installed] ******************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************************
ok: [jenkins-master-01]

TASK [Get repo Jenkins] ******************************************************************************************************************
changed: [jenkins-master-01]

TASK [Add Jenkins key] *******************************************************************************************************************
changed: [jenkins-master-01]

TASK [Install epel-release] **************************************************************************************************************
changed: [jenkins-master-01]

TASK [Install Jenkins and requirements] **************************************************************************************************
changed: [jenkins-master-01]

TASK [Ensure jenkins agents are present in known_hosts file] *****************************************************************************
# 158.160.36.140:22 SSH-2.0-OpenSSH_7.4
# 158.160.36.140:22 SSH-2.0-OpenSSH_7.4
# 158.160.36.140:22 SSH-2.0-OpenSSH_7.4
# 158.160.36.140:22 SSH-2.0-OpenSSH_7.4
# 158.160.36.140:22 SSH-2.0-OpenSSH_7.4
changed: [jenkins-master-01] => (item=jenkins-agent-01)
[WARNING]: Module remote_tmp /home/jenkins/.ansible/tmp did not exist and was created with a mode of 0700, this may cause issues when
running as another user. To avoid this, create the remote_tmp dir with the correct permissions manually

TASK [Start Jenkins] *********************************************************************************************************************
changed: [jenkins-master-01]

PLAY [Prepare jenkins agent] *************************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************************
ok: [jenkins-agent-01]

TASK [Add master publickey into authorized_key] ******************************************************************************************
changed: [jenkins-agent-01]

TASK [Create agent_dir] ******************************************************************************************************************
changed: [jenkins-agent-01]

TASK [Add docker repo] *******************************************************************************************************************
changed: [jenkins-agent-01]

TASK [Install some required] *************************************************************************************************************
changed: [jenkins-agent-01]

TASK [Update pip] ************************************************************************************************************************
changed: [jenkins-agent-01]

TASK [Install Ansible] *******************************************************************************************************************
changed: [jenkins-agent-01]

TASK [Reinstall Selinux] *****************************************************************************************************************
changed: [jenkins-agent-01]

TASK [Add local to PATH] *****************************************************************************************************************
changed: [jenkins-agent-01]

TASK [Create docker group] ***************************************************************************************************************
ok: [jenkins-agent-01]

TASK [Add jenkinsuser to dockergroup] ****************************************************************************************************
changed: [jenkins-agent-01]

TASK [Restart docker] ********************************************************************************************************************
changed: [jenkins-agent-01]

TASK [Install agent.jar] *****************************************************************************************************************
changed: [jenkins-agent-01]

PLAY RECAP *******************************************************************************************************************************
jenkins-agent-01           : ok=17   changed=14   unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
jenkins-master-01          : ok=11   changed=9    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
## Основная часть
#### 1. Сделать Freestyle Job, который будет запускать molecule test из любого вашего репозитория с ролью.


