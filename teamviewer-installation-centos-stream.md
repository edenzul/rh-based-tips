# Teamviewer installation for centos stream

**Note**
2022/03/17
- recent teamviewer version (teamviewer_15.27.3.x86_64.rpm) seems is not working in centos-8-stream.
- Therefore the software version used in this instruction is using older release teamviewer_15.15.2.x86_64.rpm

___

**Procedure**

 1. Download teamviewer.
```
wget https://dl.teamviewer.com/download/linux/version_15x/teamviewer_15.15.2.x86_64.rpm
```
 2. Install and update latest Extra Packages of Enterprise Linux (epel-release)
```
yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
yum makecache
```

 3. Install or update dependencies packages (this is optional step, only if your system require it)
```
yum install harfbuzz
yum install freetype-devel
```

 4. install teamviewer locally.
```
yum install ./teamviewer_15.15.2.x86_64.rpm 
```
___

**Installation Logs**
```
[denny@banana Downloads]$ wget https://dl.teamviewer.com/download/linux/version_15x/teamviewer_15.15.2.x86_64.rpm
--2022-03-17 23:28:59--  https://dl.teamviewer.com/download/linux/version_15x/teamviewer_15.15.2.x86_64.rpm
Resolving dl.teamviewer.com (dl.teamviewer.com)... 104.16.62.16, 104.16.63.16, 2606:4700::6810:3e10, ...
Connecting to dl.teamviewer.com (dl.teamviewer.com)|104.16.62.16|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 14731104 (14M) [application/x-redhat-package-manager]
Saving to: ‘teamviewer_15.15.2.x86_64.rpm’

teamviewer_15.15.2.x86_64.rpm        100%[====================================================================>]  14.05M  2.13MB/s    in 8.0s    

2022-03-17 23:29:08 (1.75 MB/s) - ‘teamviewer_15.15.2.x86_64.rpm’ saved [14731104/14731104]

[root@banana ~]#sudo -i

[root@banana ~]# yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
Last metadata expiration check: 0:07:41 ago on Thu 17 Mar 2022 10:57:36 PM CST.
epel-release-latest-8.noarch.rpm                                                                                   20 kB/s |  22 kB     00:01    
Dependencies resolved.
==================================================================================================================================================
 Package                              Architecture                   Version                           Repository                            Size
==================================================================================================================================================
Installing:
 epel-release                         noarch                         8-14.el8                          @commandline                          22 k

Transaction Summary
==================================================================================================================================================
Install  1 Package

Total size: 22 k
Installed size: 32 k
Is this ok [y/N]: y
Downloading Packages:
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                                                                                          1/1 
  Installing       : epel-release-8-14.el8.noarch                                                                                             1/1 
  Running scriptlet: epel-release-8-14.el8.noarch                                                                                             1/1 
  Verifying        : epel-release-8-14.el8.noarch                                                                                             1/1 

Installed:
  epel-release-8-14.el8.noarch                                                                                                                    

Complete!

[root@banana ~]# cd /home/denny/Downloads/

[root@banana Downloads]# yum install ./teamviewer_15.15.2.x86_64.rpm 
Last metadata expiration check: 0:03:29 ago on Thu 17 Mar 2022 11:26:33 PM CST.
Dependencies resolved.
==================================================================================================================================================
 Package                               Architecture             Version                                      Repository                      Size
==================================================================================================================================================
Installing:
 teamviewer                            x86_64                   15.15.2-0                                    @commandline                    14 M
Installing dependencies:
 qt5-qtlocation                        x86_64                   5.15.2-2.el8                                 appstream                      3.3 M
 qt5-qtquickcontrols                   x86_64                   5.15.2-2.el8                                 appstream                      1.1 M
 qt5-qtsensors                         x86_64                   5.15.2-2.el8                                 appstream                      220 k
 qt5-qtwebchannel                      x86_64                   5.15.2-2.el8                                 appstream                      102 k
 qt5-qtwebkit                          x86_64                   5.212.0-0.60.alpha4.el8                      epel                            13 M

Transaction Summary
==================================================================================================================================================
Install  6 Packages

Total size: 32 M
Total download size: 18 M
Installed size: 134 M
Is this ok [y/N]: y
Downloading Packages:
(1/5): qt5-qtsensors-5.15.2-2.el8.x86_64.rpm                                                                      220 kB/s | 220 kB     00:00    
(2/5): qt5-qtwebchannel-5.15.2-2.el8.x86_64.rpm                                                                   139 kB/s | 102 kB     00:00    
[MIRROR] qt5-qtwebkit-5.212.0-0.60.alpha4.el8.x86_64.rpm: Status code: 404 for http://mirrors.nipa.cloud/epel/8/Everything/x86_64/Packages/q/qt5-qtwebkit-5.212.0-0.60.alpha4.el8.x86_64.rpm (IP: 45.121.60.165)
(3/5): qt5-qtquickcontrols-5.15.2-2.el8.x86_64.rpm                                                                391 kB/s | 1.1 MB     00:02    
(4/5): qt5-qtlocation-5.15.2-2.el8.x86_64.rpm                                                                     520 kB/s | 3.3 MB     00:06    
(5/5): qt5-qtwebkit-5.212.0-0.60.alpha4.el8.x86_64.rpm                                                            621 kB/s |  13 MB     00:22    
--------------------------------------------------------------------------------------------------------------------------------------------------
Total                                                                                                             688 kB/s |  18 MB     00:26     
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Running scriptlet: teamviewer-15.15.2-0.x86_64                                                                                              1/1 
  Preparing        :                                                                                                                          1/1 
  Installing       : qt5-qtwebchannel-5.15.2-2.el8.x86_64                                                                                     1/6 
  Installing       : qt5-qtsensors-5.15.2-2.el8.x86_64                                                                                        2/6 
  Installing       : qt5-qtquickcontrols-5.15.2-2.el8.x86_64                                                                                  3/6 
  Installing       : qt5-qtlocation-5.15.2-2.el8.x86_64                                                                                       4/6 
  Installing       : qt5-qtwebkit-5.212.0-0.60.alpha4.el8.x86_64                                                                              5/6 
  Installing       : teamviewer-15.15.2-0.x86_64                                                                                              6/6 
  Running scriptlet: teamviewer-15.15.2-0.x86_64                                                                                              6/6 
gtk-update-icon-cache: Cache file created successfully.

  Verifying        : qt5-qtlocation-5.15.2-2.el8.x86_64                                                                                       1/6 
  Verifying        : qt5-qtquickcontrols-5.15.2-2.el8.x86_64                                                                                  2/6 
  Verifying        : qt5-qtsensors-5.15.2-2.el8.x86_64                                                                                        3/6 
  Verifying        : qt5-qtwebchannel-5.15.2-2.el8.x86_64                                                                                     4/6 
  Verifying        : qt5-qtwebkit-5.212.0-0.60.alpha4.el8.x86_64                                                                              5/6 
  Verifying        : teamviewer-15.15.2-0.x86_64                                                                                              6/6 

Installed:
  qt5-qtlocation-5.15.2-2.el8.x86_64             qt5-qtquickcontrols-5.15.2-2.el8.x86_64               qt5-qtsensors-5.15.2-2.el8.x86_64          
  qt5-qtwebchannel-5.15.2-2.el8.x86_64           qt5-qtwebkit-5.212.0-0.60.alpha4.el8.x86_64           teamviewer-15.15.2-0.x86_64                

Complete!
[root@banana Downloads]#
```

___

**Reference:**

 - https://www.teamviewer.com/en/download/linux/
 - https://community.teamviewer.com/English/kb/articles/30708-install-teamviewer-on-red-hat-and-centos
 - https://www.how2shout.com/how-to/how-to-install-epel-8-repository-on-centos-8-or-stream.html
 - https://linuxhint.com/install-teamviewer-centos/

