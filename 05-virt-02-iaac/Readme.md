# Домашнее задание к занятию "5.2. Применение принципов IaaC в работе с виртуальными машинами"

## Задача 1

- Опишите своими словами основные преимущества применения на практике IaaC паттернов.
- Какой из принципов IaaC является основополагающим?

### Ответ (Опишите своими словами преимущества...)
1. Сокращение времени на доставку протестированных, отлаженных, готовых, приложений потребителям, заказчикам.   
2. Стабильность среды, устранение дрейфа конфигураций. Контроль одноРОДности среды для повышения качества и скорости разработки, тестирования, доставки приложений.
3. Проверка безопасности и юзабилити (по сути - повышение качества ПО)

### Ответ (Какой из принципов IaaC...) - Идемпотентность    
### Описание с wiki:     
Идемпотентная операция в информатике — действие, многократное повторение которого эквивалентно однократному.    
Примером такой операции могут служить GET-запросы в протоколе HTTP.     
По спецификации, сервер должен возвращать идентичные ответы на идентичные GET-запросы (при условии, что ресурс не изменился).     
Это позволяет корректно кэшировать эти ответы, снижая нагрузку на сеть.   

### Своими словами:    
Код в скриптах и программы перед выполнением тех или иных действий должны делать проверки различных условий, чтобы:    
1. Не тратить ресурсы (к примеру время - самый главный ресурс) на лишние, ненужные операции и задачи. (переустановку/перезапись уже установленного и работающего ПО)    
2. Проверять состояние тех или иных систем, и если по условию всё нормально - ни чего не менять, заканчивать успехом, выходить.   
3. Попросту - убедившись, что состояние системы не требует вмешательства и всё хорошо, не делать лишних телодвижений.     

## Задача 2

- Чем Ansible выгодно отличается от других систем управление конфигурациями?    
- Какой, на ваш взгляд, метод работы систем конфигурации более надёжный push или pull?    

### Ответ (Чем Ansible выгодно отличается...)
Простой доступ к управлению инфраструктурой без дополнительных агентов.    
Управление, конфигурация серверов Unix/Linux по SSH (ssh есть и может запускаться на любом сервере)    
Управление, конфигурация серверов  Windows через PowerShell.    
Много доп. расширений на любой "вкус и цвет". Можно собрать в систему управления только то, что действительно необходимо.   
Масштабируемость. Исключает в работе как режимы "из пушки по воробъям", так и "из рогатки по слону".     

### Ответ (Какой, на ваш взгляд...)
На мой взгляд надёжность одинакова.   
В различных системах и конфигурациях требуется свой метод.  

## Задача 3

Установить на личный компьютер:

- VirtualBox
- Vagrant
- Ansible

*Приложить вывод команд установленных версий каждой из программ, оформленный в markdown.*
```bash
root@vagrant-srv:~# date
Mon 29 Aug 2022 03:18:48 PM UTC

root@vagrant-srv:~# virtualbox --help
Oracle VM VirtualBox VM Selector v6.1.34_Ubuntu
(C) 2005-2022 Oracle Corporation
All rights reserved.
No special options.

root@vagrant-srv:~# vagrant --version
Vagrant 2.3.0

root@vagrant-srv:~# ansible --version
ansible 2.9.6
  config file = /etc/ansible/ansible.cfg
  configured module search path = ['/root/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3/dist-packages/ansible
  executable location = /usr/bin/ansible
  python version = 3.8.10 (default, Jun 22 2022, 20:18:18) [GCC 9.4.0]

```
## Задача 4 (*)

Воспроизвести практическую часть лекции самостоятельно.

- Создать виртуальную машину.
- Зайти внутрь ВМ, убедиться, что Docker установлен с помощью команды
```
docker ps
```
### Решение:    
Установил vagrant на вирт. машину (vagrant-srv) с Ubuntu под ESXI.     
Установил Oracle VM на вирт. машину (vagrant-srv)   
Запустил вирт. машину на платформе Oracle VM VirtualBox командой ***vagrant up***.     
Использовал упрощённую конфигурацию из 2 файлов:    

#### Первый файл: Vagrantfile
```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

ENV['VAGRANT_DEFAULT_PROVIDER'] = 'virtualbox'
Vagrant.configure("2") do |config|
########## VM definition ############
  config.vm.define "srv" do |config|
  config.vm.hostname = "server1"
  config.vm.box = "generic/ubuntu2004"
  config.vm.box_check_update = false
  config.vm.provider :virtualbox do |v|
    v.memory = 4096
    v.cpus = 2
  config.vm.provision "shell", inline: <<-SHELL
    hostnamectl set-hostname server1
    apt-get -y update
    apt-get -y upgrade
    apt-get -y install ansible
    shutdown -r
    SHELL
  end
  config.vm.provision :ansible do |ansible|
    ansible.limit = "all"
    ansible.playbook = "provision.yaml"
  end
 end
end

```
#### Второй файл: provision.yaml
```yaml
---
- hosts: all
  become: yes
  become_method: sudo
  tasks:
  - name: "Update OS"
    package:
     name: '*'
     state: latest
  - name: Install Basic packages
    package:
      name: ['docker', 'docker-compose', 'vim' ]
...


```
В результате vagrant установил ubuntu2004 на Oracle VM , запустил ansible.       
Аnsible довёл состояние вирт. машины до: "наличие установленных и обновлённых пакетов docker, docker-compose, vim".    

Зашел внутрь ВМ server1 (***команда vagrant ssh***)        
Проверил весию docker.    
Проверил его работу, запустив контейнер "Hello World".  

   
![ibb-img](https://i.ibb.co/7QmpBCz/Screenshot-6.png)

### Решение задачи на гипервизоре ESXI  
Использовал плагин vagrant-vmware-esxi.
https://github.com/josenk/vagrant-vmware-esxi

```bash
vagrant plugin install vagrant-vmware-esxi
vagrant plugin list
vagrant version

vagrant-vmware-esxi (2.5.5, global)

```


#### Первый файл: Vagrantfile

```yaml
# -*- mode: ruby -*-
# vi: set ft=ruby :
#
Vagrant.configure('2') do |config|
  config.vm.box = 'generic/ubuntu2004'
  config.vm.synced_folder('.', '/vagrant')
  config.vm.provider :vmware_esxi do |esxi|
    esxi.esxi_hostname = '192.168.1.15'
    esxi.esxi_username = 'root'
    esxi.esxi_password = '**********'
    esxi.esxi_hostport = 22
    esxi.guest_name = 'server1'
    esxi.guest_username = 'vagrant'
    esxi.guest_memsize = '4096'
    esxi.guest_numvcpus = '2'
    esxi.guest_nic_type = 'vmxnet3'
    esxi.guest_boot_disk_size = 30
    end
  config.vm.provision "shell", inline: <<-SHELL
    hostnamectl set-hostname server1
    end
    apt -y update
    apt -y upgrade
    apt -y install ansible curl wget mc net-tools
    shutdown -r now
    SHELL
  config.vm.provision "ansible" do |ansible|
    ansible.verbose = "v"
    ansible.inventory_path = "./ansible/inventory"
    ansible.playbook = "./ansible/provision.yaml"
    ansible.become = true
    ansible.extra_vars = { ansible_user: 'vagrant' }
  end
end

```  

#### Второй файл: provision.yaml
```yaml
---
- hosts: all
  become: yes
  become_method: sudo
  tasks:
  - name: "Update OS"
    package:
     name: '*'
     state: latest
  - name: Install Basic packages
    package:
      name: ['docker', 'docker-compose', 'vim', 'net-tools' ]
      #  - name: Installing docker
      #    shell: curl -fsSL get.docker.com -o get-docker.sh && chmod +x get-docker.sh && ./get-docker.sh
  - name: Add the current user to docker group
    user: name=vagrant append=yes groups=docker
...


```

