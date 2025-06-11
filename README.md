# OSPF
Складываем все файлы в одно место и запускам `vagrant up`

vagrant@router3:~$ ansible-playbook -i /vagrant/ansible/hosts /vagrant/ansible/provision.yml --extra-vars "host_key_checking=false" --limit all

PLAY [OSPF] ************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************
ok: [router1]
ok: [router3]
ok: [router2]

TASK [install base tools] **********************************************************************************************
ok: [router3]
ok: [router1]
ok: [router2]

TASK [disable ufw service] *********************************************************************************************
ok: [router2]
ok: [router1]
ok: [router3]

TASK [add gpg frrouting.org] *******************************************************************************************
ok: [router2]
ok: [router3]
ok: [router1]

TASK [add frr repo] ****************************************************************************************************
ok: [router2]
ok: [router3]
ok: [router1]

TASK [install FRR packages] ********************************************************************************************
ok: [router2]
ok: [router1]
ok: [router3]

TASK [set up forward packages across routers] **************************************************************************
ok: [router2]
ok: [router3]
ok: [router1]

TASK [set up asynchronous routing] *************************************************************************************
ok: [router1]
ok: [router2]
ok: [router3]

TASK [base set up OSPF] ************************************************************************************************
ok: [router1]
ok: [router2]
ok: [router3]

TASK [set up OSPF] *****************************************************************************************************
changed: [router2]
changed: [router1]
changed: [router3]

TASK [restart FRR] *****************************************************************************************************
changed: [router1]
changed: [router2]
changed: [router3]

PLAY RECAP *************************************************************************************************************
router1                    : ok=11   changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
router2                    : ok=11   changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
router3                    : ok=11   changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
