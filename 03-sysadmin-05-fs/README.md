---
## Ответы на домашнее задание "3.5. Файловые системы" 
---
                    
<strong>1. Узнайте о sparse (разряженных) файлах.</strong>     

После прочтения Wiki поэксперементировал с  разряжённым файлом.  
    
dd if=/dev/zero of=./sparse-file bs=1 count=0 seek=5G   
0+0 records in  
0+0 records out 
0 bytes copied, 3.3308e-05 s, 0.0 kB/s  

vagrant vagrant 5368709120 Jul  2 18:48 sparse-file     
    
du -hs sparse-file      
0       sparse-file     
    
stat sparse-file    

File: sparse-file   
Size: 5368709120      Blocks: 0          IO Block: 4096   regular file      
Device: 801h/2049d      Inode: 544         Links: 1     
Access: (0664/-rw-rw-r--)  Uid: ( 1000/ vagrant)   Gid: ( 1000/ vagrant)    
Access: 2022-07-02 18:48:30.770996488 +0000     
Modify: 2022-07-02 18:48:30.770996488 +0000     
Change: 2022-07-02 18:48:30.770996488 +0000     
Birth: 2022-07-02 18:48:30.770996488 +0000  

Перенаправил вывод разряхенного файла в обычный:    
cat sparse-file > simple-file   
du -hs simple-file  
5.1G    simple-file 

stat simple-file    
File: simple-file   
Size: 5368709120      Blocks: 10485768   IO Block: 4096   regular file  
Device: 801h/2049d      Inode: 1860        Links: 1 
Access: (0664/-rw-rw-r--)  Uid: ( 1000/ vagrant)   Gid: ( 1000/ vagrant)    
Access: 2022-07-02 18:52:55.612666690 +0000     
Modify: 2022-07-02 18:53:02.972602112 +0000     
Change: 2022-07-02 18:53:02.972602112 +0000     
Birth: 2022-07-02 18:52:55.612666690 +0000     


