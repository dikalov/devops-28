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
```
PLAY [Destroy] *****************************************************************

TASK [Destroy molecule instance(s)] ******************************************** changed: [localhost] => (item=instance)

TASK [Wait for instance(s) deletion to complete] ******************************* FAILED - RETRYING: Wait for instance(s) deletion to complete (300 retries left). ok: [localhost] => (item=instance)

TASK [Delete docker networks(s)] ***********************************************

PLAY RECAP ********************************************************************* localhost : ok=2 changed=1 unreachable=0 failed=0 skipped=1 rescued=0 ignored=0

INFO Running default > syntax

playbook: /opt/jenkins_agent/workspace/Freestyle Job/roles/vector-role/molecule/default/converge.yml /usr/local/lib/python3.6/site-packages/ansible/parsing/vault/init.py:44: CryptographyDeprecationWarning: Python 3.6 is no longer supported by the Python core team. Therefore, support for it is deprecated in cryptography and will be removed in a future release. from cryptography.exceptions import InvalidSignature INFO Running default > create /usr/local/lib/python3.6/site-packages/ansible/parsing/vault/init.py:44: CryptographyDeprecationWarning: Python 3.6 is no longer supported by the Python core team. Therefore, support for it is deprecated in cryptography and will be removed in a future release. from cryptography.exceptions import InvalidSignature [WARNING]: Collection community.docker does not support Ansible version 2.10.17

PLAY [Create] ******************************************************************

TASK [Log into a Docker registry] ********************************************** skipping: [localhost] => (item=None) skipping: [localhost]

TASK [Check presence of custom Dockerfiles] ************************************ ok: [localhost] => (item={'image': 'docker.io/pycontribs/centos:7', 'name': 'instance', 'pre_build_image': True})

TASK [Create Dockerfiles from image names] ************************************* skipping: [localhost] => (item={'image': 'docker.io/pycontribs/centos:7', 'name': 'instance', 'pre_build_image': True})

TASK [Discover local Docker images] ******************************************** ok: [localhost] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional result was False', 'item': {'image': 'docker.io/pycontribs/centos:7', 'name': 'instance', 'pre_build_image': True}, 'ansible_loop_var': 'item', 'i': 0, 'ansible_index_var': 'i'})

TASK [Build an Ansible compatible image (new)] ********************************* skipping: [localhost] => (item=molecule_local/docker.io/pycontribs/centos:7)

TASK [Create docker network(s)] ************************************************

TASK [Determine the CMD directives] ******************************************** ok: [localhost] => (item={'image': 'docker.io/pycontribs/centos:7', 'name': 'instance', 'pre_build_image': True})

TASK [Create molecule instance(s)] ********************************************* changed: [localhost] => (item=instance)

TASK [Wait for instance(s) creation to complete] ******************************* FAILED - RETRYING: Wait for instance(s) creation to complete (300 retries left). FAILED - RETRYING: Wait for instance(s) creation to complete (299 retries left). FAILED - RETRYING: Wait for instance(s) creation to complete (298 retries left). FAILED - RETRYING: Wait for instance(s) creation to complete (297 retries left). FAILED - RETRYING: Wait for instance(s) creation to complete (296 retries left). FAILED - RETRYING: Wait for instance(s) creation to complete (295 retries left). FAILED - RETRYING: Wait for instance(s) creation to complete (294 retries left). changed: [localhost] => (item={'started': 1, 'finished': 0, 'ansible_job_id': '703475122553.18178', 'results_file': '/home/jenkins/.ansible_async/703475122553.18178', 'changed': True, 'failed': False, 'item': {'image': 'docker.io/pycontribs/centos:7', 'name': 'instance', 'pre_build_image': True}, 'ansible_loop_var': 'item'})

PLAY RECAP ********************************************************************* localhost : ok=5 changed=2 unreachable=0 failed=0 skipped=4 rescued=0 ignored=0

INFO Running default > prepare WARNING Skipping, prepare playbook not configured. INFO Running default > converge

PLAY [Converge] ****************************************************************

TASK [Gathering Facts] ********************************************************* /usr/local/lib/python3.6/site-packages/ansible/parsing/vault/init.py:44: CryptographyDeprecationWarning: Python 3.6 is no longer supported by the Python core team. Therefore, support for it is deprecated in cryptography and will be removed in a future release. from cryptography.exceptions import InvalidSignature [WARNING]: Collection community.docker does not support Ansible version 2.10.17 ok: [instance]

TASK [Include vector-role] *****************************************************

TASK [vector-role : Get Vector distrib | CentOS] ******************************* [WARNING]: Collection community.docker does not support Ansible version 2.10.17 changed: [instance]

TASK [vector-role : Get Vector distrib | Ubuntu] ******************************* skipping: [instance]

TASK [vector-role : Install Vector packages | CentOS] ************************** [WARNING]: Collection community.docker does not support Ansible version 2.10.17 changed: [instance]

TASK [vector-role : Install Vector packages | Ubuntu] ************************** skipping: [instance]

TASK [vector-role : Deploy config Vector] ************************************** [WARNING]: Collection community.docker does not support Ansible version 2.10.17 [WARNING]: The value "0" (type int) was converted to "u'0'" (type string). If this does not look like what you expect, quote the entire value to ensure it does not change. changed: [instance]

TASK [vector-role : Creates directory] ***************************************** [WARNING]: Collection community.docker does not support Ansible version 2.10.17 changed: [instance]

TASK [vector-role : Create systemd unit Vector] ******************************** [WARNING]: Collection community.docker does not support Ansible version 2.10.17 changed: [instance]

TASK [vector-role : Start Vector service] ************************************** skipping: [instance]

RUNNING HANDLER [vector-role : Start Vector service] *************************** skipping: [instance]

PLAY RECAP ********************************************************************* instance : ok=6 changed=5 unreachable=0 failed=0 skipped=4 rescued=0 ignored=0

INFO Running default > idempotence

PLAY [Converge] ****************************************************************

TASK [Gathering Facts] ********************************************************* /usr/local/lib/python3.6/site-packages/ansible/parsing/vault/init.py:44: CryptographyDeprecationWarning: Python 3.6 is no longer supported by the Python core team. Therefore, support for it is deprecated in cryptography and will be removed in a future release. from cryptography.exceptions import InvalidSignature [WARNING]: Collection community.docker does not support Ansible version 2.10.17 ok: [instance]

TASK [Include vector-role] *****************************************************

TASK [vector-role : Get Vector distrib | CentOS] ******************************* [WARNING]: Collection community.docker does not support Ansible version 2.10.17 ok: [instance]

TASK [vector-role : Get Vector distrib | Ubuntu] ******************************* skipping: [instance]

TASK [vector-role : Install Vector packages | CentOS] ************************** [WARNING]: Collection community.docker does not support Ansible version 2.10.17 ok: [instance]

TASK [vector-role : Install Vector packages | Ubuntu] ************************** skipping: [instance]

TASK [vector-role : Deploy config Vector] ************************************** [WARNING]: Collection community.docker does not support Ansible version 2.10.17 [WARNING]: The value "0" (type int) was converted to "u'0'" (type string). If this does not look like what you expect, quote the entire value to ensure it does not change. ok: [instance]

TASK [vector-role : Creates directory] ***************************************** [WARNING]: Collection community.docker does not support Ansible version 2.10.17 ok: [instance]

TASK [vector-role : Create systemd unit Vector] ******************************** [WARNING]: Collection community.docker does not support Ansible version 2.10.17 ok: [instance]

TASK [vector-role : Start Vector service] ************************************** skipping: [instance]

PLAY RECAP ********************************************************************* instance : ok=6 changed=0 unreachable=0 failed=0 skipped=3 rescued=0 ignored=0

INFO Idempotence completed successfully. INFO Running default > side_effect WARNING Skipping, side effect playbook not configured. INFO Running default > verify INFO Running Ansible Verifier

PLAY [Verify] ******************************************************************

TASK [Get Vector version] ****************************************************** /usr/local/lib/python3.6/site-packages/ansible/parsing/vault/init.py:44: CryptographyDeprecationWarning: Python 3.6 is no longer supported by the Python core team. Therefore, support for it is deprecated in cryptography and will be removed in a future release. from cryptography.exceptions import InvalidSignature [WARNING]: Collection community.docker does not support Ansible version 2.10.17 ok: [instance]

TASK [Assert Vector instalation] *********************************************** [WARNING]: Collection community.docker does not support Ansible version 2.10.17 ok: [instance] => { "changed": false, "msg": "All assertions passed" }

TASK [Validation Vector configuration] ***************************************** [WARNING]: Collection community.docker does not support Ansible version 2.10.17 ok: [instance]

TASK [Assert Vector validate config] ******************************************* [WARNING]: Collection community.docker does not support Ansible version 2.10.17 ok: [instance] => { "changed": false, "msg": "All assertions passed" }

PLAY RECAP ********************************************************************* instance : ok=4 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0

INFO Verifier completed successfully. INFO Running default > cleanup WARNING Skipping, cleanup playbook not configured. INFO Running default > destroy /usr/local/lib/python3.6/site-packages/ansible/parsing/vault/init.py:44: CryptographyDeprecationWarning: Python 3.6 is no longer supported by the Python core team. Therefore, support for it is deprecated in cryptography and will be removed in a future release. from cryptography.exceptions import InvalidSignature [WARNING]: Collection community.docker does not support Ansible version 2.10.17

PLAY [Destroy] *****************************************************************

TASK [Destroy molecule instance(s)] ******************************************** changed: [localhost] => (item=instance)

TASK [Wait for instance(s) deletion to complete] ******************************* FAILED - RETRYING: Wait for instance(s) deletion to complete (300 retries left). changed: [localhost] => (item=instance)

TASK [Delete docker networks(s)] ***********************************************

PLAY RECAP ********************************************************************* localhost : ok=2 changed=2 unreachable=0 failed=0 skipped=1 rescued=0 ignored=0

INFO Pruning extra files from scenario ephemeral directory Finished: SUCCESS
```
#### 2. Сделать Declarative Pipeline Job, который будет запускать molecule test из любого вашего репозитория с ролью.
![image](https://github.com/dikalov/devops-28/assets/126553776/34d105a5-597c-4da1-8df0-73fc64a2a076)
```
PLAY [Destroy] *****************************************************************

TASK [Destroy molecule instance(s)] ******************************************** changed: [localhost] => (item=instance)

TASK [Wait for instance(s) deletion to complete] ******************************* FAILED - RETRYING: Wait for instance(s) deletion to complete (300 retries left). ok: [localhost] => (item=instance)

TASK [Delete docker networks(s)] ***********************************************

PLAY RECAP ********************************************************************* localhost : ok=2 changed=1 unreachable=0 failed=0 skipped=1 rescued=0 ignored=0

INFO Running default > syntax

playbook: /opt/jenkins_agent/workspace/DeclarativePipelineJob/roles/vector-role/molecule/default/converge.yml /usr/local/lib/python3.6/site-packages/ansible/parsing/vault/init.py:44: CryptographyDeprecationWarning: Python 3.6 is no longer supported by the Python core team. Therefore, support for it is deprecated in cryptography and will be removed in a future release. from cryptography.exceptions import InvalidSignature INFO Running default > create /usr/local/lib/python3.6/site-packages/ansible/parsing/vault/init.py:44: CryptographyDeprecationWarning: Python 3.6 is no longer supported by the Python core team. Therefore, support for it is deprecated in cryptography and will be removed in a future release. from cryptography.exceptions import InvalidSignature [WARNING]: Collection community.docker does not support Ansible version 2.10.17

PLAY [Create] ******************************************************************

TASK [Log into a Docker registry] ********************************************** skipping: [localhost] => (item=None) skipping: [localhost]

TASK [Check presence of custom Dockerfiles] ************************************ ok: [localhost] => (item={'image': 'docker.io/pycontribs/centos:7', 'name': 'instance', 'pre_build_image': True})

TASK [Create Dockerfiles from image names] ************************************* skipping: [localhost] => (item={'image': 'docker.io/pycontribs/centos:7', 'name': 'instance', 'pre_build_image': True})

TASK [Discover local Docker images] ******************************************** ok: [localhost] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional result was False', 'item': {'image': 'docker.io/pycontribs/centos:7', 'name': 'instance', 'pre_build_image': True}, 'ansible_loop_var': 'item', 'i': 0, 'ansible_index_var': 'i'})

TASK [Build an Ansible compatible image (new)] ********************************* skipping: [localhost] => (item=molecule_local/docker.io/pycontribs/centos:7)

TASK [Create docker network(s)] ************************************************

TASK [Determine the CMD directives] ******************************************** ok: [localhost] => (item={'image': 'docker.io/pycontribs/centos:7', 'name': 'instance', 'pre_build_image': True})

TASK [Create molecule instance(s)] ********************************************* changed: [localhost] => (item=instance)

TASK [Wait for instance(s) creation to complete] ******************************* FAILED - RETRYING: Wait for instance(s) creation to complete (300 retries left). changed: [localhost] => (item={'started': 1, 'finished': 0, 'ansible_job_id': '70894573170.449', 'results_file': '/home/jenkins/.ansible_async/70894573170.449', 'changed': True, 'failed': False, 'item': {'image': 'docker.io/pycontribs/centos:7', 'name': 'instance', 'pre_build_image': True}, 'ansible_loop_var': 'item'})

PLAY RECAP ********************************************************************* localhost : ok=5 changed=2 unreachable=0 failed=0 skipped=4 rescued=0 ignored=0

INFO Running default > prepare WARNING Skipping, prepare playbook not configured. INFO Running default > converge

PLAY [Converge] ****************************************************************

TASK [Gathering Facts] ********************************************************* /usr/local/lib/python3.6/site-packages/ansible/parsing/vault/init.py:44: CryptographyDeprecationWarning: Python 3.6 is no longer supported by the Python core team. Therefore, support for it is deprecated in cryptography and will be removed in a future release. from cryptography.exceptions import InvalidSignature [WARNING]: Collection community.docker does not support Ansible version 2.10.17 ok: [instance]

TASK [Include vector-role] *****************************************************

TASK [vector-role : Get Vector distrib | CentOS] ******************************* [WARNING]: Collection community.docker does not support Ansible version 2.10.17 changed: [instance]

TASK [vector-role : Get Vector distrib | Ubuntu] ******************************* skipping: [instance]

TASK [vector-role : Install Vector packages | CentOS] ************************** [WARNING]: Collection community.docker does not support Ansible version 2.10.17 changed: [instance]

TASK [vector-role : Install Vector packages | Ubuntu] ************************** skipping: [instance]

TASK [vector-role : Deploy config Vector] ************************************** [WARNING]: Collection community.docker does not support Ansible version 2.10.17 [WARNING]: The value "0" (type int) was converted to "u'0'" (type string). If this does not look like what you expect, quote the entire value to ensure it does not change. changed: [instance]

TASK [vector-role : Creates directory] ***************************************** [WARNING]: Collection community.docker does not support Ansible version 2.10.17 changed: [instance]

TASK [vector-role : Create systemd unit Vector] ******************************** [WARNING]: Collection community.docker does not support Ansible version 2.10.17 changed: [instance]

TASK [vector-role : Start Vector service] ************************************** skipping: [instance]

RUNNING HANDLER [vector-role : Start Vector service] *************************** skipping: [instance]

PLAY RECAP ********************************************************************* instance : ok=6 changed=5 unreachable=0 failed=0 skipped=4 rescued=0 ignored=0

INFO Running default > idempotence

PLAY [Converge] ****************************************************************

TASK [Gathering Facts] ********************************************************* /usr/local/lib/python3.6/site-packages/ansible/parsing/vault/init.py:44: CryptographyDeprecationWarning: Python 3.6 is no longer supported by the Python core team. Therefore, support for it is deprecated in cryptography and will be removed in a future release. from cryptography.exceptions import InvalidSignature [WARNING]: Collection community.docker does not support Ansible version 2.10.17 ok: [instance]

TASK [Include vector-role] *****************************************************

TASK [vector-role : Get Vector distrib | CentOS] ******************************* [WARNING]: Collection community.docker does not support Ansible version 2.10.17 ok: [instance]

TASK [vector-role : Get Vector distrib | Ubuntu] ******************************* skipping: [instance]

TASK [vector-role : Install Vector packages | CentOS] ************************** [WARNING]: Collection community.docker does not support Ansible version 2.10.17 ok: [instance]

TASK [vector-role : Install Vector packages | Ubuntu] ************************** skipping: [instance]

TASK [vector-role : Deploy config Vector] ************************************** [WARNING]: Collection community.docker does not support Ansible version 2.10.17 [WARNING]: The value "0" (type int) was converted to "u'0'" (type string). If this does not look like what you expect, quote the entire value to ensure it does not change. ok: [instance]

TASK [vector-role : Creates directory] ***************************************** [WARNING]: Collection community.docker does not support Ansible version 2.10.17 ok: [instance]

TASK [vector-role : Create systemd unit Vector] ******************************** [WARNING]: Collection community.docker does not support Ansible version 2.10.17 ok: [instance]

TASK [vector-role : Start Vector service] ************************************** skipping: [instance]

PLAY RECAP ********************************************************************* instance : ok=6 changed=0 unreachable=0 failed=0 skipped=3 rescued=0 ignored=0

INFO Idempotence completed successfully. INFO Running default > side_effect WARNING Skipping, side effect playbook not configured. INFO Running default > verify INFO Running Ansible Verifier

PLAY [Verify] ******************************************************************

TASK [Get Vector version] ****************************************************** /usr/local/lib/python3.6/site-packages/ansible/parsing/vault/init.py:44: CryptographyDeprecationWarning: Python 3.6 is no longer supported by the Python core team. Therefore, support for it is deprecated in cryptography and will be removed in a future release. from cryptography.exceptions import InvalidSignature [WARNING]: Collection community.docker does not support Ansible version 2.10.17 ok: [instance]

TASK [Assert Vector instalation] *********************************************** [WARNING]: Collection community.docker does not support Ansible version 2.10.17 ok: [instance] => { "changed": false, "msg": "All assertions passed" }

TASK [Validation Vector configuration] ***************************************** [WARNING]: Collection community.docker does not support Ansible version 2.10.17 ok: [instance]

TASK [Assert Vector validate config] ******************************************* [WARNING]: Collection community.docker does not support Ansible version 2.10.17 ok: [instance] => { "changed": false, "msg": "All assertions passed" }

PLAY RECAP ********************************************************************* instance : ok=4 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0

INFO Verifier completed successfully. INFO Running default > cleanup WARNING Skipping, cleanup playbook not configured. INFO Running default > destroy /usr/local/lib/python3.6/site-packages/ansible/parsing/vault/init.py:44: CryptographyDeprecationWarning: Python 3.6 is no longer supported by the Python core team. Therefore, support for it is deprecated in cryptography and will be removed in a future release. from cryptography.exceptions import InvalidSignature [WARNING]: Collection community.docker does not support Ansible version 2.10.17

PLAY [Destroy] *****************************************************************

TASK [Destroy molecule instance(s)] ******************************************** changed: [localhost] => (item=instance)

TASK [Wait for instance(s) deletion to complete] ******************************* FAILED - RETRYING: Wait for instance(s) deletion to complete (300 retries left). changed: [localhost] => (item=instance)

TASK [Delete docker networks(s)] ***********************************************

PLAY RECAP ********************************************************************* localhost : ok=2 changed=2 unreachable=0 failed=0 skipped=1 rescued=0 ignored=0

INFO Pruning extra files from scenario ephemeral directory [Pipeline] cleanWs [WS-CLEANUP] Deleting project workspace... [WS-CLEANUP] Deferred wipeout is used... [WS-CLEANUP] done [Pipeline] } [Pipeline] // dir [Pipeline] } [Pipeline] // stage [Pipeline] } [Pipeline] // node [Pipeline] End of Pipeline Finished: SUCCESS
```
#### 3. Перенести Declarative Pipeline в репозиторий в файл Jenkinsfile.
[Файл Jenkinsfile](https://github.com/dikalov/devops-28/blob/main/08-ansible-04-role%20/playbook/roles/vector/Jenkinsfile)

#### 4. Создать Multibranch Pipeline на запуск Jenkinsfile из репозитория.
![image](https://github.com/dikalov/devops-28/assets/126553776/d6156213-20e6-4de7-ad3a-44e2040d7ca7)
```
PLAY [Destroy] *****************************************************************

TASK [Destroy molecule instance(s)] ******************************************** changed: [localhost] => (item=instance)

TASK [Wait for instance(s) deletion to complete] ******************************* FAILED - RETRYING: Wait for instance(s) deletion to complete (300 retries left). ok: [localhost] => (item=instance)

TASK [Delete docker networks(s)] ***********************************************

PLAY RECAP ********************************************************************* localhost : ok=2 changed=1 unreachable=0 failed=0 skipped=1 rescued=0 ignored=0

INFO Running default > syntax

playbook: /opt/jenkins_agent/workspace/Multibranch_Pipeline_main/roles/vector-role/molecule/default/converge.yml /usr/local/lib/python3.6/site-packages/ansible/parsing/vault/init.py:44: CryptographyDeprecationWarning: Python 3.6 is no longer supported by the Python core team. Therefore, support for it is deprecated in cryptography and will be removed in a future release. from cryptography.exceptions import InvalidSignature INFO Running default > create /usr/local/lib/python3.6/site-packages/ansible/parsing/vault/init.py:44: CryptographyDeprecationWarning: Python 3.6 is no longer supported by the Python core team. Therefore, support for it is deprecated in cryptography and will be removed in a future release. from cryptography.exceptions import InvalidSignature [WARNING]: Collection community.docker does not support Ansible version 2.10.17

PLAY [Create] ******************************************************************

TASK [Log into a Docker registry] ********************************************** skipping: [localhost] => (item=None) skipping: [localhost]

TASK [Check presence of custom Dockerfiles] ************************************ ok: [localhost] => (item={'image': 'docker.io/pycontribs/centos:7', 'name': 'instance', 'pre_build_image': True})

TASK [Create Dockerfiles from image names] ************************************* skipping: [localhost] => (item={'image': 'docker.io/pycontribs/centos:7', 'name': 'instance', 'pre_build_image': True})

TASK [Discover local Docker images] ******************************************** ok: [localhost] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional result was False', 'item': {'image': 'docker.io/pycontribs/centos:7', 'name': 'instance', 'pre_build_image': True}, 'ansible_loop_var': 'item', 'i': 0, 'ansible_index_var': 'i'})

TASK [Build an Ansible compatible image (new)] ********************************* skipping: [localhost] => (item=molecule_local/docker.io/pycontribs/centos:7)

TASK [Create docker network(s)] ************************************************

TASK [Determine the CMD directives] ******************************************** ok: [localhost] => (item={'image': 'docker.io/pycontribs/centos:7', 'name': 'instance', 'pre_build_image': True})

TASK [Create molecule instance(s)] ********************************************* changed: [localhost] => (item=instance)

TASK [Wait for instance(s) creation to complete] ******************************* FAILED - RETRYING: Wait for instance(s) creation to complete (300 retries left). changed: [localhost] => (item={'started': 1, 'finished': 0, 'ansible_job_id': '163475297165.3894', 'results_file': '/home/jenkins/.ansible_async/163475297165.3894', 'changed': True, 'failed': False, 'item': {'image': 'docker.io/pycontribs/centos:7', 'name': 'instance', 'pre_build_image': True}, 'ansible_loop_var': 'item'})

PLAY RECAP ********************************************************************* localhost : ok=5 changed=2 unreachable=0 failed=0 skipped=4 rescued=0 ignored=0

INFO Running default > prepare WARNING Skipping, prepare playbook not configured. INFO Running default > converge

PLAY [Converge] ****************************************************************

TASK [Gathering Facts] ********************************************************* /usr/local/lib/python3.6/site-packages/ansible/parsing/vault/init.py:44: CryptographyDeprecationWarning: Python 3.6 is no longer supported by the Python core team. Therefore, support for it is deprecated in cryptography and will be removed in a future release. from cryptography.exceptions import InvalidSignature [WARNING]: Collection community.docker does not support Ansible version 2.10.17 ok: [instance]

TASK [Include vector-role] *****************************************************

TASK [vector-role : Get Vector distrib | CentOS] ******************************* [WARNING]: Collection community.docker does not support Ansible version 2.10.17 changed: [instance]

TASK [vector-role : Get Vector distrib | Ubuntu] ******************************* skipping: [instance]

TASK [vector-role : Install Vector packages | CentOS] ************************** [WARNING]: Collection community.docker does not support Ansible version 2.10.17 changed: [instance]

TASK [vector-role : Install Vector packages | Ubuntu] ************************** skipping: [instance]

TASK [vector-role : Deploy config Vector] ************************************** [WARNING]: Collection community.docker does not support Ansible version 2.10.17 [WARNING]: The value "0" (type int) was converted to "u'0'" (type string). If this does not look like what you expect, quote the entire value to ensure it does not change. changed: [instance]

TASK [vector-role : Creates directory] ***************************************** [WARNING]: Collection community.docker does not support Ansible version 2.10.17 changed: [instance]

TASK [vector-role : Create systemd unit Vector] ******************************** [WARNING]: Collection community.docker does not support Ansible version 2.10.17 changed: [instance]

TASK [vector-role : Start Vector service] ************************************** skipping: [instance]

RUNNING HANDLER [vector-role : Start Vector service] *************************** skipping: [instance]

PLAY RECAP ********************************************************************* instance : ok=6 changed=5 unreachable=0 failed=0 skipped=4 rescued=0 ignored=0

INFO Running default > idempotence

PLAY [Converge] ****************************************************************

TASK [Gathering Facts] ********************************************************* /usr/local/lib/python3.6/site-packages/ansible/parsing/vault/init.py:44: CryptographyDeprecationWarning: Python 3.6 is no longer supported by the Python core team. Therefore, support for it is deprecated in cryptography and will be removed in a future release. from cryptography.exceptions import InvalidSignature [WARNING]: Collection community.docker does not support Ansible version 2.10.17 ok: [instance]

TASK [Include vector-role] *****************************************************

TASK [vector-role : Get Vector distrib | CentOS] ******************************* [WARNING]: Collection community.docker does not support Ansible version 2.10.17 ok: [instance]

TASK [vector-role : Get Vector distrib | Ubuntu] ******************************* skipping: [instance]

TASK [vector-role : Install Vector packages | CentOS] ************************** [WARNING]: Collection community.docker does not support Ansible version 2.10.17 ok: [instance]

TASK [vector-role : Install Vector packages | Ubuntu] ************************** skipping: [instance]

TASK [vector-role : Deploy config Vector] ************************************** [WARNING]: Collection community.docker does not support Ansible version 2.10.17 [WARNING]: The value "0" (type int) was converted to "u'0'" (type string). If this does not look like what you expect, quote the entire value to ensure it does not change. ok: [instance]

TASK [vector-role : Creates directory] ***************************************** [WARNING]: Collection community.docker does not support Ansible version 2.10.17 ok: [instance]

TASK [vector-role : Create systemd unit Vector] ******************************** [WARNING]: Collection community.docker does not support Ansible version 2.10.17 ok: [instance]

TASK [vector-role : Start Vector service] ************************************** skipping: [instance]

PLAY RECAP ********************************************************************* instance : ok=6 changed=0 unreachable=0 failed=0 skipped=3 rescued=0 ignored=0

INFO Idempotence completed successfully. INFO Running default > side_effect WARNING Skipping, side effect playbook not configured. INFO Running default > verify INFO Running Ansible Verifier

PLAY [Verify] ******************************************************************

TASK [Get Vector version] ****************************************************** /usr/local/lib/python3.6/site-packages/ansible/parsing/vault/init.py:44: CryptographyDeprecationWarning: Python 3.6 is no longer supported by the Python core team. Therefore, support for it is deprecated in cryptography and will be removed in a future release. from cryptography.exceptions import InvalidSignature [WARNING]: Collection community.docker does not support Ansible version 2.10.17 ok: [instance]

TASK [Assert Vector instalation] *********************************************** [WARNING]: Collection community.docker does not support Ansible version 2.10.17 ok: [instance] => { "changed": false, "msg": "All assertions passed" }

TASK [Validation Vector configuration] ***************************************** [WARNING]: Collection community.docker does not support Ansible version 2.10.17 ok: [instance]

TASK [Assert Vector validate config] ******************************************* [WARNING]: Collection community.docker does not support Ansible version 2.10.17 ok: [instance] => { "changed": false, "msg": "All assertions passed" }

PLAY RECAP ********************************************************************* instance : ok=4 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0

INFO Verifier completed successfully. INFO Running default > cleanup WARNING Skipping, cleanup playbook not configured. INFO Running default > destroy /usr/local/lib/python3.6/site-packages/ansible/parsing/vault/init.py:44: CryptographyDeprecationWarning: Python 3.6 is no longer supported by the Python core team. Therefore, support for it is deprecated in cryptography and will be removed in a future release. from cryptography.exceptions import InvalidSignature [WARNING]: Collection community.docker does not support Ansible version 2.10.17

PLAY [Destroy] *****************************************************************

TASK [Destroy molecule instance(s)] ******************************************** changed: [localhost] => (item=instance)

TASK [Wait for instance(s) deletion to complete] ******************************* FAILED - RETRYING: Wait for instance(s) deletion to complete (300 retries left). changed: [localhost] => (item=instance)

TASK [Delete docker networks(s)] ***********************************************

PLAY RECAP ********************************************************************* localhost : ok=2 changed=2 unreachable=0 failed=0 skipped=1 rescued=0 ignored=0

INFO Pruning extra files from scenario ephemeral directory [Pipeline] cleanWs [WS-CLEANUP] Deleting project workspace... [WS-CLEANUP] Deferred wipeout is used... [WS-CLEANUP] done [Pipeline] } [Pipeline] // dir [Pipeline] } [Pipeline] // stage [Pipeline] } [Pipeline] // withEnv [Pipeline] } [Pipeline] // node [Pipeline] End of Pipeline

Could not update commit status, please check if your scan credentials belong to a member of the organization or a collaborator of the repository and repo:status scope is selected

GitHub has been notified of this commit’s build result

Finished: SUCCESS
```
#### 5. Создать Scripted Pipeline, наполнить его скриптом из pipeline.


