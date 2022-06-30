---
## Ответы на домашнее задание "3.3. Операционные системы, лекция 1" 
---
                    
<strong>1. Какой системный вызов делает команда cd?</strong>  
    
strace /bin/bash -c 'cd /tmp' > strace.log 2>&1; cat strace.log | grep -w chdir       

Ответ:  
chdir("/tmp") = 0   
    
<strong>2. Используя strace выясните, где находится база данных file...</strong>   

man magic     
описание    
На этой странице руководства задокументирован формат magic files, используемый командой file(1) версии 5.39. Команда file(1) определяет тип файла
используя, среди прочих тестов, тест на наличие в файле определенных “магических шаблонов”. База данных этих “магических шаблонов” обычно находится в
двоичном файле в /usr/share/misc/magic.mgc или в каталоге файлов фрагментов магических шаблонов с исходным текстом в /usr/share/misc/magic. Конкретная база данных определяет, какие шаблоны должны быть проверены, какое сообщение или тип MIME для печати, если обнаружен определенный шаблон, и дополнительную информацию для извлечения из досье.     
    
strace file 2&> tmp.txt; cat tmp.txt | grep magic.mgc   
    
stat("/home/vagrant/.magic.mgc", 0x7ffe570aa7e0) = -1 ENOENT (Нет такого файла или каталога)    
openat(AT_FDCWD, "/etc/magic.mgc", O_RDONLY) = -1 ENOENT (Нет такого файла или каталога)    
openat(AT_FDCWD, "/usr/share/misc/magic.mgc", O_RDONLY) = 3     
    
ответ: /usr/share/misc/magic.mgc    
    
<strong>3. Основываясь на знаниях о перенаправлении потоков предложите способ обнуления открытого удаленного файла</strong>    
Запускаю процесс (PID=1416) пинга с выводом результатов в ping.log      
ping 192.168.1.1 >> ping.log &      
удаляю ping.log (rm ping.log)   
проверяю открытые процессом файлы       
sudo lsof -p 1416 | grep deleted        
ping    1416 vagrant    1w   REG    8,1    14942 17655 /home/vagrant/ping.log (deleted)     
Узнаю файловый дескриптор        
sudo ls -la /proc/1416/fd   
l-wx------ 1 root    root    64 Jun 29 16:52 1 -> '/home/vagrant/ping.log (deleted)'       
обнуляю удалённый файл:         
cat /dev/null | sudo tee /proc/1416/fd/1    
ping    1416 vagrant    1w   REG    8,1      10 17655 /home/vagrant/ping.log (deleted)  
размер удалённого файла обнулился   
Процесс остался в рабочем состоянии.    
    
<strong>4. Занимают ли зомби-процессы какие-то ресурсы в ОС (CPU, RAM, IO)?</strong>   

Зомби-процессы каких-либо ресурсов не занимают, кроме информации в списке процессов.    
    
<strong>5. На какие файлы вы увидели вызовы группы open за первую секунду работы утилиты?</strong>      

sudo /usr/sbin/opensnoop-bpfcc

3 warnings generated.   
PID    COMM               FD ERR PATH   
620    irqbalance          6   0 /proc/interrupts   
620    irqbalance          6   0 /proc/stat 
620    irqbalance          6   0 /proc/irq/20/smp_affinity  
620    irqbalance          6   0 /proc/irq/0/smp_affinity   
620    irqbalance          6   0 /proc/irq/1/smp_affinity   
620    irqbalance          6   0 /proc/irq/4/smp_affinity   
620    irqbalance          6   0 /proc/irq/8/smp_affinity   
620    irqbalance          6   0 /proc/irq/12/smp_affinity  
620    irqbalance          6   0 /proc/irq/14/smp_affinity  
620    irqbalance          6   0 /proc/irq/15/smp_affinity  
573    systemd-network    19   0 /run/systemd/netif/lldp/.#32Kbga1  
573    systemd-network    25   0 /run/systemd/netif/lldp/.#32Kbga1  
573    systemd-network    26   0 /run/systemd/netif/lldp/3  
1      systemd            12   0 /proc/573/cgroup   
620    irqbalance          6   0 /proc/interrupts   
620    irqbalance          6   0 /proc/stat     


<strong>6. Какой системный вызов использует uname -a?</strong>       
    
системный вызов: uname  
strace uname -a &> temp && cat temp | grep uname        
execve("/usr/bin/uname", ["uname", "-a"], 0x7ffc8fef7f58 /* 24 vars */) = 0     
uname({sysname="Linux", nodename="ubuntu-jammy", ...}) = 0      
uname({sysname="Linux", nodename="ubuntu-jammy", ...}) = 0      
uname({sysname="Linux", nodename="ubuntu-jammy", ...}) = 0      
    
cat /proc/version   
Linux version 5.15.0-40-generic (buildd@lcy02-amd64-047) (gcc (Ubuntu 11.2.0-19ubuntu1) 11.2.0, GNU ld (GNU Binutils for Ubuntu) 2.38) #43-Ubuntu SMP Wed Jun 15 12:54:21 UTC 2022  
    
man proc        
This string identifies the kernel version that is currently running.  It includes the  contents  of  /proc/sys/kernel/ostype, /proc/sys/kernel/osrelease, and /proc/sys/kernel/version.     
    
<strong>7. Чем отличается последовательность команд через ; и через && в bash?  
Есть ли смысл использовать в bash &&, если применить set -e?</strong>         

Вся последовательность команд через ; будет выполнена, невзирая на результат предшевствующей команды (0 или 1)

ls /несущестующий; cd /невнятный; ls -l /home/  
ls: cannot access '/несущестующий': No such file or directory   
-bash: cd: /невнятный: No such file or directory    
total 8      
drwxr-x--- 3 ubuntu  ubuntu  4096 Jun 18 09:03 ubuntu   
drwxr-x--- 6 vagrant vagrant 4096 Jun 30 16:17 vagrant  

Конструкция && позволит выполнить последовательность команд в том случае, если результат предшедствующего выполнения был 0 (команда отработала без ошибок)      
ls /несущестующий && cd /невнятный && ls -l /home/      
ls: cannot access '/несущестующий': No such file or directory   

set --help   
-e  Exit immediately if a command exits with a non-zero status. (Немедленный выход, если команда завершается с ненулевым статусом.)   

При задействовании конструкции set -е смысла использовать в bash &&, на мой взгляд, нет.   

<strong>8. Из каких опций состоит режим bash set -euxo pipefail и почему его хорошо было бы использовать в сценариях?</strong>  
    
set --help  
    -e  Exit immediately if a command exits with a non-zero status. (прекращает выполнение скрипта если команда завершилась ошибкой, выводит в stderr строку с ошибкой)         
    -u  Treat unset variables as an error when substituting. (прекращает выполнение скрипта, если встретилась несуществующая переменная)        
    -x  xtrace  (выводит выполняемые команды в stdout перед выполненинем)       
    -o pipefail the return value of a pipeline is the status of the last command to exit with a non-zero status, or zero if no command exited with a non-zero status  (прекращает выполнение скрипта, даже если одна из частей пайпа завершилась ошибкой)      

man bash | grep -w pipefail     

pipefail возвращаемое значение конвейера — это статус последней команды для выхода с ненулевым статусом или ноль,   
если ни одна команда не вышла с ненулевым статусом      

Данный набор параметров, на мой взгляд, добавляет гарантии на успешное получение результата. Того, что требовалось от скрипта.  


<strong>9. Используя -o stat для ps, определите, какой наиболее часто встречающийся статус у процессов в системе.</strong>  
    
