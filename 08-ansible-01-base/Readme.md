# Домашнее задание к занятию "1. Введение в Ansible"

## Подготовка к выполнению
1. Установите ansible версии 2.10 или выше.         
#### ansible --version

```bash
ansible [core 2.14.1]
  config file = None
  configured module search path = ['/home/*****/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/local/lib/python3.10/dist-packages/ansible
  ansible collection location = /home/*****/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/local/bin/ansible
  python version = 3.10.6 (main, Nov 14 2022, 16:10:14) [GCC 11.3.0] (/usr/bin/python3)
  jinja version = 3.0.3
  libyaml = True

```

2. Создайте свой собственный публичный репозиторий на github с произвольным именем.
3. Скачайте [playbook](./playbook/) из репозитория с домашним заданием и перенесите его в свой репозиторий.

## Основная часть
1. Попробуйте запустить playbook на окружении из `test.yml`, зафиксируйте какое значение имеет факт `some_fact` для указанного хоста при выполнении playbook'a.

#### ansible2:~/work/playbook$ ansible-playbook -i inventory/test.yml site.yml

```bash

PLAY [Print os facts] *******************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************
ok: [localhost]

TASK [Print OS] *************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Ubuntu"
}

TASK [Print fact] ***********************************************************************************************************************************
ok: [localhost] => {
    "msg": 12
}

PLAY RECAP ******************************************************************************************************************************************
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0


```

#### ansible2:~/work/playbook$ ansible-inventory -i inventory/test.yml site.yml --list

```json
{
    "_meta": {
        "hostvars": {
            "localhost": {
                "ansible_connection": "local",
                "some_fact": 12
            }
        }
    },
    "all": {
        "children": [
            "inside",
            "ungrouped"
        ]
    },
    "inside": {
        "hosts": [
            "localhost"
        ]
    }
}

```

2. Найдите файл с переменными (group_vars) в котором задаётся найденное в первом пункте значение и поменяйте его на 'all default fact'.

#### ansible2:~/work/playbook$ cat group_vars/all/examp.yml

```yaml
---
  some_fact: 'all default fact'
```        

3. Воспользуйтесь подготовленным (используется `docker`) или создайте собственное окружение для проведения дальнейших испытаний.    

Запустил два контейнера:    

#### docker-compose.yml  

```yaml
version: '3'
services:
  centos7:
    image: centos
    container_name: centos7
    restart: always
    entrypoint: "sleep infinity"

  ubuntu:
    image: pycontribs/ubuntu
    container_name: ubuntu
    restart: always
    entrypoint: "sleep infinity"

```

####    ansible2:~/work/os$ docker ps

```bash
CONTAINER ID   IMAGE     COMMAND            CREATED         STATUS         PORTS     NAMES
22bb5ad93575   centos    "sleep infinity"   7 seconds ago   Up 6 seconds             centos7
32af3ca187e8   ubuntu    "sleep infinity"   7 seconds ago   Up 6 seconds             ubuntu

```


4. Проведите запуск playbook на окружении из `prod.yml`. Зафиксируйте полученные значения `some_fact` для каждого из `managed host`.    

#### ansible2:~/work/playbook$ ansible-playbook -i inventory/prod.yml site.yml

```bash
PLAY [Print os facts] *******************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************
ok: [centos7]
ok: [ubuntu]

TASK [Print OS] *************************************************************************************************************************************
ok: [centos7] => {
    "msg": "CentOS"
}
ok: [ubuntu] => {
    "msg": "Ubuntu"
}

TASK [Print fact] ***********************************************************************************************************************************
ok: [centos7] => {
    "msg": "el"
}
ok: [ubuntu] => {
    "msg": "deb"
}

PLAY RECAP ******************************************************************************************************************************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```

5. Добавьте факты в `group_vars` каждой из групп хостов так, чтобы для `some_fact` получились следующие значения: для `deb` - 'deb default fact', для `el` - 'el default fact'.

#### ansible-inventory -i inventory/prod.yml --list
```json
{
    "_meta": {
        "hostvars": {
            "centos7": {
                "ansible_connection": "docker",
                "some_fact": "el default fact"
            },
            "ubuntu": {
                "ansible_connection": "docker",
                "some_fact": "deb default fact"
            }
        }
    },
    "all": {
        "children": [
            "deb",
            "el",
            "ungrouped"
        ]
    },
    "deb": {
        "hosts": [
            "ubuntu"
        ]
    },
    "el": {
        "hosts": [
            "centos7"
        ]
    }
}

```

6.  Повторите запуск playbook на окружении `prod.yml`. Убедитесь, что выдаются корректные значения для всех хостов.
#### ansible2:~/work/playbook$ ansible-playbook -i inventory/prod.yml site.yml  
    
```bash
PLAY [Print os facts] *******************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************
ok: [centos7]
ok: [ubuntu]

TASK [Print OS] *************************************************************************************************************************************
ok: [centos7] => {
    "msg": "CentOS"
}
ok: [ubuntu] => {
    "msg": "Ubuntu"
}

TASK [Print fact] ***********************************************************************************************************************************
ok: [centos7] => {
    "msg": "el default fact"
}
ok: [ubuntu] => {
    "msg": "deb default fact"
}

PLAY RECAP ******************************************************************************************************************************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```

7. При помощи `ansible-vault` зашифруйте факты в `group_vars/deb` и `group_vars/el` с паролем `netology`.       

Зашифровал строки 'deb default fact', 'el default fact'и заменил нешифрованные значения в файлах examp.yml:    

#### ansible2:~/work/playbook$ cat group_vars/el/examp.yml

```
---
  some_fact: "el default fact"      

ansible-vault encrypt_string
New Vault password:
Confirm New Vault password:
Reading plaintext input from stdin. (ctrl-d to end input, twice if your content does not already have a newline)
"el default fact"
Encryption successful
!vault |
          $ANSIBLE_VAULT;1.1;AES256
          35366630336136393632376465353430383563396538636663333666323735663734356265633534
          6633346435373461366535396637623836343066666131350a353964666130626634656134356533
          36346561643539383034393734666536373430343932626230633134623935356534636663646364
          3938393739396465620a626437623937363861373939363565313433303462636339613231623839
          37333438643863313638633861666561353335623563383261663566313962633361
```

#### ansible2:~/work/playbook$ cat group_vars/deb/examp.yml

```
---
  some_fact: "deb default fact"

ansible-vault encrypt_string
New Vault password:
Confirm New Vault password:
Reading plaintext input from stdin. (ctrl-d to end input, twice if your content does not already have a newline)
"deb default fact"
Encryption successful
!vault |
          $ANSIBLE_VAULT;1.1;AES256
          31366133383961633164636433396635663366363061373164636566383439623363626334376666
          6633353365346266343062663239643738343333383563630a356137316261346463323435643834
          62613663303438333861653135356239613561666262323939666365613566353731636164643766
          6330373862333263330a633331393864313266376436663861333866383666396538393935656336
          36346565656530653566353433353162383731616134396339326364323539323866
```
#### cat group_vars/el/examp.yml && cat group_vars/deb/examp.yml

```
---
  some_fact: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          35366630336136393632376465353430383563396538636663333666323735663734356265633534
          6633346435373461366535396637623836343066666131350a353964666130626634656134356533
          36346561643539383034393734666536373430343932626230633134623935356534636663646364
          3938393739396465620a626437623937363861373939363565313433303462636339613231623839
          37333438643863313638633861666561353335623563383261663566313962633361

---
  some_fact: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          31366133383961633164636433396635663366363061373164636566383439623363626334376666
          6633353365346266343062663239643738343333383563630a356137316261346463323435643834
          62613663303438333861653135356239613561666262323939666365613566353731636164643766
          6330373862333263330a633331393864313266376436663861333866383666396538393935656336
          36346565656530653566353433353162383731616134396339326364323539323866

```


8. Запустите playbook на окружении `prod.yml`. При запуске `ansible` должен запросить у вас пароль. Убедитесь в работоспособности.
Запустил ansible-playbook с запросом пароля:        

#### ansible2:~/work/playbook$ ansible-playbook -i inventory/prod.yml site.yml --ask-vault-pass
```bash
Vault password:

PLAY [Print os facts] *****************************************************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************************************
ok: [centos7]
ok: [ubuntu]

TASK [Print OS] ***********************************************************************************************************************************
ok: [centos7] => {
    "msg": "CentOS"
}
ok: [ubuntu] => {
    "msg": "Ubuntu"
}

TASK [Print fact] *********************************************************************************************************************************
ok: [centos7] => {
    "msg": "el default fact"
}
ok: [ubuntu] => {
    "msg": "deb default fact"
}

PLAY RECAP ****************************************************************************************************************************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```


9. Посмотрите при помощи `ansible-doc` список плагинов для подключения.     
Выберите подходящий для работы на `control node`.       

#### ansible2:~/work/playbook$ ansible-doc -t connection -l

```
ansible2:~/work/playbook$ ansible-doc -t connection -l
ansible.builtin.local          execute on controller
ansible.builtin.paramiko_ssh   Run tasks via python ssh (paramiko)
ansible.builtin.psrp           Run tasks over Microsoft PowerShell Remoting Protocol
ansible.builtin.ssh            connect via SSH client binary
ansible.builtin.winrm          Run tasks over Microsoft's WinRM
ansible.netcommon.grpc         Provides a persistent connection using the gRPC protocol
ansible.netcommon.httpapi      Use httpapi to run command on network appliances
ansible.netcommon.libssh       Run tasks using libssh for ssh connection
ansible.netcommon.netconf      Provides a persistent connection using the netconf protocol
ansible.netcommon.network_cli  Use network_cli to run command on network appliances
ansible.netcommon.persistent   Use a persistent unix socket for connection
community.aws.aws_ssm          execute via AWS Systems Manager
community.docker.docker        Run tasks in docker containers
community.docker.docker_api    Run tasks in docker containers
community.docker.nsenter       execute on host running controller container
community.general.chroot       Interact with local chroot
community.general.funcd        Use funcd to connect to target
community.general.iocage       Run tasks in iocage jails
community.general.jail         Run tasks in jails
community.general.lxc          Run tasks in lxc containers via lxc python library
community.general.lxd          Run tasks in lxc containers via lxc CLI
community.general.qubes        Interact with an existing QubesOS AppVM
community.general.saltstack    Allow ansible to piggyback on salt minions
community.general.zone         Run tasks in a zone instance
community.libvirt.libvirt_lxc  Run tasks in lxc containers via libvirt
community.libvirt.libvirt_qemu Run tasks on libvirt/qemu virtual machines
community.okd.oc               Execute tasks in pods running on OpenShift
community.vmware.vmware_tools  Execute tasks inside a VM via VMware Tools
containers.podman.buildah      Interact with an existing buildah container
containers.podman.podman       Interact with an existing podman container
kubernetes.core.kubectl        Execute tasks in pods running on Kubernetes

```

Для работы на control node используем ansible.builtin.local (в случае окружения из test.yml)        
#### ansible-doc -t connection ansible.builtin.local    

```
ANSIBLE.BUILTIN.LOCAL   
        
This connection plugin allows ansible to execute tasks on the Ansible 'controller' instead of on a remotehost.
```        
Для работы на control node с окружением из prod.yml используем community.docker.docker     
#### ansible-doc -t connection community.docker.docker     
 
```
COMMUNITY.DOCKER.DOCKER   

Run commands or put/fetch files to an existing docker container. Uses the Docker CLI to execute commands in the container. If you prefer to directly connect to the Docker daemon, use the community.docker.docker_api connection plugin.
```

10. В `prod.yml` добавьте новую группу хостов с именем  `local`, в ней разместите localhost с необходимым типом подключения.
#### ansible2:~/work/playbook$ cat inventory/prod.yml
```yaml
---
  el:
    hosts:
      centos7:
        ansible_connection: docker
  deb:
    hosts:
      ubuntu:
        ansible_connection: docker
  local:
    hosts:
      localhost:
        ansible_connection: local
```

11. Запустите playbook на окружении `prod.yml`. При запуске `ansible` должен запросить у вас пароль. Убедитесь что факты `some_fact` для каждого из хостов определены из верных `group_vars`.

#### ansible2:~/work/playbook$ ansible-playbook -i inventory/prod.yml site.yml --ask-vault-pass
```
Vault password:

PLAY [Print os facts] *****************************************************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************************************
ok: [localhost]
ok: [ubuntu]
ok: [centos7]

TASK [Print OS] ***********************************************************************************************************************************
ok: [centos7] => {
    "msg": "CentOS"
}
ok: [ubuntu] => {
    "msg": "Ubuntu"
}
ok: [localhost] => {
    "msg": "Ubuntu"
}

TASK [Print fact] *********************************************************************************************************************************
ok: [localhost] => {
    "msg": "all default fact"
}
ok: [centos7] => {
    "msg": "el default fact"
}
ok: [ubuntu] => {
    "msg": "deb default fact"
}

PLAY RECAP ****************************************************************************************************************************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

12. Заполните `README.md` ответами на вопросы. Сделайте `git push` в ветку `master`. В ответе отправьте ссылку на ваш открытый репозиторий с изменённым `playbook` и заполненным `README.md`.

## Необязательная часть

1. При помощи `ansible-vault` расшифруйте все зашифрованные файлы с переменными.
2. Зашифруйте отдельное значение `PaSSw0rd` для переменной `some_fact` паролем `netology`. Добавьте полученное значение в `group_vars/all/exmp.yml`.
3. Запустите `playbook`, убедитесь, что для нужных хостов применился новый `fact`.
4. Добавьте новую группу хостов `fedora`, самостоятельно придумайте для неё переменную. В качестве образа можно использовать [этот](https://hub.docker.com/r/pycontribs/fedora).
5. Напишите скрипт на bash: автоматизируйте поднятие необходимых контейнеров, запуск ansible-playbook и остановку контейнеров.
6. Все изменения должны быть зафиксированы и отправлены в вашей личный репозиторий.

---
