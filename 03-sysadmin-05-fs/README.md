---
## Ответы на домашнее задание "3.5. Файловые системы" 
---
                    
<strong>1. Узнайте о sparse (разряженных) файлах.</strong>     

После прочтения Wiki поэксперементировал с  разряжённым файлом. 

dd if=/dev/zero of=./sparse-file bs=1 count=0 seek=20G      
vagrant vagrant 21474836480 Jul  2 18:40 sparse-file    

du -hs sparse-file  
0       sparse-file     
    
stat sparse-file    
  File: sparse-file 
  Size: 21474836480     Blocks: 0          IO Block: 4096   regular file    
Device: 801h/2049d      Inode: 544         Links: 1 
Access: (0664/-rw-rw-r--)  Uid: ( 1000/ vagrant)   Gid: ( 1000/ vagrant)    
Access: 2022-07-02 18:39:28.711720253 +0000 
Modify: 2022-07-02 18:40:30.199186035 +0000 
Change: 2022-07-02 18:40:30.199186035 +0000 
Birth: 2022-07-02 18:39:28.711720253 +0000 

