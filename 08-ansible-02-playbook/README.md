# Домашнее задание к занятию "2. Работа с Playbook"

## Подготовка к выполнению

1. (Необязательно) Изучите, что такое [clickhouse](https://www.youtube.com/watch?v=fjTNS2zkeBs) и [vector](https://www.youtube.com/watch?v=CgEhyffisLY)
#### Просмотрено, спасибо за Алексею за видео и проделанную работу!    
2. Создайте свой собственный (или используйте старый) публичный репозиторий на github с произвольным именем.        
#### [Есть такой](https://github.com/devops-run/devops-netology/tree/main/08-ansible-02-playbook/playbook)     
3. Скачайте [playbook](./playbook/) из репозитория с домашним заданием и перенесите его в свой репозиторий.        
#### Сделано
4. Подготовьте хосты в соответствии с группами из предподготовленного playbook.
#### Сделано. Подготовлено две вирт. машины (Fedora 37 Server Edition, AlmaLinux 9.1)

## Основная часть

1. Приготовьте свой собственный inventory файл `prod.yml`.
#### Сделано: [prod.yml](playbook/inventory/prod.yml), Отредактирован нерабочий [site.yml](playbook/site.yml)               
2. Допишите playbook: нужно сделать ещё один play, который устанавливает и настраивает [vector](https://vector.dev).    
### Часть prod.yml, отвечающая за Vector
```yaml
# Загрузка и установка Vector
- name: Vector Setup
  hosts: vector
  tasks:
    - name: Get Vector
      become: true
      ansible.builtin.get_url:
        # yamllint disable-line rule:line-length
        url: "https://packages.timber.io/vector/0.26.0/vector-{{ vector_version }}.x86_64.rpm"
        dest: "./vector-{{ vector_version }}.x86_64.rpm"
      #############################################
    - name: Install Vector
      become: true
      ansible.builtin.yum:
        disable_gpg_check: true
        name:
          - vector-{{ vector_version }}.x86_64.rpm
      ############################################
    - name: Enable vector
      become: true
      ansible.builtin.shell: "systemctl enable vector.service"
      ############################################
    - name: Start vector service
      become: true
      ansible.builtin.service:
        name: vector.service
        state: restarted

```
5. Запустите `ansible-lint site.yml` и исправьте ошибки, если они есть.     
#### Протестировал ansible-lint site.yml, исправил ошибки с помощью [yamlfixer](https://github.com/opt-nc/yamlfixer) --summary site.yml  
6. Попробуйте запустить playbook на этом окружении с флагом `--check`.      

#### ansible2:~/work/playbook$ ansible-playbook -i inventory/prod.yml site.yml --check  
```bash
PLAY RECAP ********************************************************************************************************************************************
clickhouse-01              : ok=11   changed=4    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0
vector-01                  : ok=4    changed=1    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
```

7. Запустите playbook на `prod.yml` окружении с флагом `--diff`. Убедитесь, что изменения на системе произведены.   
#### ansible2:~/work/playbook$ ansible-playbook -i inventory/prod.yml site.yml --diff       
```bash
PLAY RECAP ********************************************************************************************************************************************
clickhouse-01              : ok=13   changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
vector-01                  : ok=5    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
8. Повторно запустите playbook с флагом `--diff` и убедитесь, что playbook идемпотентен.    
#### ansible2:~/work/playbook$ ansible-playbook -i inventory/prod.yml site.yml --diff       
```bash
PLAY RECAP ********************************************************************************************************************************************
clickhouse-01              : ok=13   changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
vector-01                  : ok=5    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```
10. Готовый playbook выложите в свой репозиторий, поставьте тег `08-ansible-02-playbook` на фиксирующий коммит, в ответ предоставьте ссылку на него.    
---
