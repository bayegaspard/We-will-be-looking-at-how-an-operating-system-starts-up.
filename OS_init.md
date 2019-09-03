## How an operating system starts up
### 1. Loading the OS
### 1.1 What is an UEFI OS loader and where does the Ubuntu OS loader reside on the system? 
> ### 1.1.0 What is an UEFI OS loader 
- To define UEFI OS loaders, first consider the diagram below.
 ![](https://i.imgur.com/dTkY7xF.png)
- The UEFI OS loader is a bootloader which loads the kernel/OS based on the UEFI specifications.
- It is comes in place when the firmware(EFI enabled) hands over control to it during booting.
- When the boatloader is loaded and is in control, it uses the only memory given to it by the UEFI enabled firmware. In sumary, It uses EFI specs to perform its duties like : 
     - It must use the proper EFI interfaces to obtain access to the bus specific-resources. i.e , I/O and memory-mapped device registers must be accessed through the proper bus specific I/O calls like those that an EFI driver would perform.
     - Be stored on the EFI System Partition with file system FAT32 (source: https://wiki.osdev.org/UEFI) 
     - Partition type be similar to C12A7328-F81F-11D2-BA4B-00A0C93EC93B.
     - Be stored in a PE (Portable Executable) like .efi format.
 (source: https://wiki.osdev.org/UEFI and https://wiki.osdev.org/PE).
 (source :https://www.intel.ru/content/dam/doc/product-specification/efi-v1-10-specification.pdf, page 2-4)
  - While during OS loading , if the OS loader experiences a problem, it sends a service Exit() code call and return control to the UEFI firmware.
  - This exit() code contains both the error code and exit data to be returned.

![](https://i.imgur.com/nrzSYHA.png)

>  ### 1.1.1 Where does the Ubuntu OS loader reside on the system?
- The UEFI enabled firmware locate the GPT partition entry with a specific ID , and name <code>esp(EFI system parition)</code>.
![](https://i.imgur.com/w1bB78s.png)

(source: https://askubuntu.com/questions/827491/is-separate-efi-boot-partition-required)

- This partition contain the bootloader which is located in <code> /boot/efi/EFI/ubuntu </code>.

![](https://i.imgur.com/hyykpwt.png)

- In this directory, we have the <code>shimx64.efi</code>  which is a relatively simple program that provides a way to boot on a computer with Secure Boot active while <code>grubx64.efi</code> which is an EFI (Extensible Firmware Interface) file which definitely launch GRUB.If shimx64.efi realises that grub does not come from a signed source, it won't load it.
(source: https://askubuntu.com/questions/342365/what-is-the-difference-between-grubx64-and-shimx64)
- We also have the <code>grub.cfg</code> file in this same directory which generally  the config file used by the grub .
![](https://i.imgur.com/ZVZqGjI.png)

(source: https://www.happyassassin.net/2014/01/25/uefi-boot-how-does-that-actually-work-then/)

### 2. Describe in order all the steps required for booting the computer (until the OS loader starts running.)
##### A-) BIOS
- If we consider starting from the booting process of ubuntu as a brief number of steps, we realise that everything starts when we press the power button on our computer.
- From this, the power goes to the ROM(EEPROM=Electrical Erasable programable ROM) which contains the BIOS(or system BIO or ROM BIOS).
- The BIOS perform some integrity check called POST(Power on Self Test) which is the process which check systems harware to know if everything is ok for the next step.
- The configurations for this are stored in the CMOS for further use.
- Sometimes we press F12 or F9 or F2 onour key board depending on the PC/Manufacturer we are having to get to the BIOS.
- The BIOS then look for the bootloader program.
- The next step depend on if we are on UEFI mode or Legacy BIOS.
##### B-) If UEFI
- The UEFI enabled firmware locate the GPT partition entry with a specific ID , and name <code>esp(EFI system parition)</code>.
![](https://i.imgur.com/w1bB78s.png)

(source: https://askubuntu.com/questions/827491/is-separate-efi-boot-partition-required)

- This partition contain the bootloader which is located in <code> /boot/efi/EFI/ubuntu </code>.

![](https://i.imgur.com/hyykpwt.png)

- In this directory, we have the <code>shimx64.efi</code>  which is a relatively simple program that provides a way to boot on a computer with Secure Boot active while <code>grubx64.efi</code> which is an EFI (Extensible Firmware Interface) file which definitely launch GRUB.
(source : https://blog.uncooperative.org/blog/2014/02/06/the-efi-system-partition/)
- If shimx64.efi realises that grub does not come from a signed source, it won't load it.
(source: https://askubuntu.com/questions/342365/what-is-the-difference-between-grubx64-and-shimx64)
- We also have the <code>grub.cfg</code> file in this same directory which generally generally the config file used by the grub .
![](https://i.imgur.com/ZVZqGjI.png)

(source: https://www.happyassassin.net/2014/01/25/uefi-boot-how-does-that-actually-work-then/)

#### C-) In the case of the old legacy systems,

- For legacy BIOS, the BIOS loads and execute the Master Boot record(MBR) found on the first partition of the drive usually, sda or hda.
![](https://i.imgur.com/B3aF64u.png)
- It occupies the first 512MB space of the drive.
- The MBR is devided into 3 secitions with each section having its main function.But the main function of the MBR, is to load the bootloader and transfer control to it.
![](https://i.imgur.com/ftw363J.jpg)


- The bootloader is the program which leads to the operating system loading because it point the path to the kernel via the bootmanager.
- For Ubuntu , it uses GRUB(Grand Unified bootloader) also called PUPA.Though there are many other bootloaders like LILO, EFI Boot systems for EFI systems etc.
(source: https://www.researchgate.net/publication/295010710_Booting_an_Intel_System_Architecture)
- GRUB has the knowledge of the filesystem but LILO no.
- The grub config file(grub.cfg) is located in the /boot/grub .
![](https://i.imgur.com/GS83IRC.png)
(source : (https://www.thegeekstuff.com/2011/02/linux-boot-process/))
- The grub bootloader loads the OS kernel in this case VMlinuz and initial ramdisk(initrd image) both located in the /boot/vmlinuz... and /boot/initrd... as precised by the grub.cfg file.(see picture below)
![](https://i.imgur.com/9lv8pt1.png)
##### D. login stage
- This run the OS and we have the loging screen where we input user name and password to login.


### 3. What is the purpose of the GRUB boot loader in a UEFI system?
- In UEFI systems, the GRUB bootloader is able to locate, understand and mount the ext4 file system.Then loads the kernel image based on UEFI specs, verifying sources for valid signatures.
- In UEFI systems, it is first validated by the UEFI enabled firmwares, then the SHIMx64.efi for valid signatures.
(source https://www.happyassassin.net/2014/01/25/uefi-boot-how-does-that-actually-work-then/)
- The bootloader is located at the esp(EFI system partition).
- This partition contain the bootloader which is located in <code> /boot/efi/EFI/ubuntu </code>.

![](https://i.imgur.com/hyykpwt.png)
(source : (http://www.rodsbooks.com/efi-bootloaders/efistub.html))
- The grub is also acting as a boot manager as it gives the capability of selecting which Kernel to boot from in the boot menu during multi boot booting.
### 4. How does the Ubuntu OS loader load the GRUB boot loader?
- For this question, lets consider the output of the command below:
<code>efibootmgr -v</code>
![](https://i.imgur.com/SyY3hku.png)
- From the above picture, we realise that the BootOrder 0000 is pointing to ubuntu entry having the esp partition on HD1.
- So the firmware selects this partition and executes the efi file located in <code>/EFI/ubuntu/shimx64.efi</code>.
- As we probably know, the EFI/ubuntu/grubx64.efi on the EFI System Partition (ESP) is the GRUB binary, while EFI/ubuntu/shimx64.efi is the binary for shim.
(source: https://en.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface)
-  The shimx64.efi is a simple computer program which makes booting to the next step possible with computers having secure boots active.
-   Since we are using UEFI, an unsigned version of GRUB won't lauch.Since signing with microsoft keys is not possible.
-   In the real world, shim registers itself with the firmware and then launches a program called grubx64.efi found in the same directory as seen in the diagram below.
![](https://i.imgur.com/UnEgAXW.png)

- This means that on a computer without Secure Boot (such as a Mac), launching shimx64.efi is just like launching grubx64.efi. 
- On a computer with Secure Boot active, launching shimx64.efi should result in GRUB starting up, whereas launching grubx64.efi directly probably won't work.
(source : https://askubuntu.com/questions/342365/what-is-the-difference-between-grubx64-and-shimx64)
### 5. Explain how the GRUB boot loader, in turn, loads and run the kernel by answering these 3 questions:
##### (a) What type of filesystem is the kernel on?
- First , analyzing our current system ,using the command <code>sudo fdisk -l /dev/sda</code> , we have the following : 
![](https://i.imgur.com/N99qm5B.png)
- From the above picture , we realise that there is /dev/sda1 which is of type EFI system located on BootOrder 0000, HD1, esp partition.
- Then we also see /dev/sda2 which contains our linux filesystem.
- If we further analyze the disk filesystem using the command <code>df -Th</code>
![](https://i.imgur.com/2jbd4zw.png)
##### - We realise that the /dev/sda2 which host our linux kernel is <code>ext4</code>.
(source: https://www.tutorialspoint.com/unix/unix-file-system)
##### (b) What type(s) of filesystem does UEFI support?
- Based on the UEFI specs, The EFI system partition(esp) needs to be formatted with a file system whose specification is based on the FAT file system ; therefore, the file system specification is independent from the original FAT specification.
- Using the command <code>sudo df -Th</code>, we can see that the efi partition is using VFAT, in our specific case, FAT32 filesystem.
![](https://i.imgur.com/38dF1ze.png)

(https://en.wikipedia.org/wiki/EFI_system_partition)
- Based on the UEFI specs, the UEFI enabled firmware looks and select partition whose globally unique identifier (GUID) for the EFI system partition in the GUID Partition Table (GPT) scheme is C12A7328-F81F-11D2-BA4B-00A0C93EC93B, while its ID in the master boot record (MBR) partition-table scheme is 0xEF. 
- Both GPT- and MBR-partitioned disks can contain an EFI system partition, as UEFI firmware is required to support both partitioning schemes.
-  Also, <code>El Torito</code>  bootable format for CD-ROMs and DVDs is supported.
-  <code>CDFS</code>  to boot data from a CD.
-  In history till now, linux kernel resided in many paritions.Some of which are : 
1. ext
2. ext2
3. ext3
4. ext4
5. npfs
6. iso9660
7. JFS
8. minix
9. msdos
10. ncpfs
11. nfs
12. UFS
13. ntfs
14. proc
15. Reiserfs
16. smb
17. sysv 
18. sysv
19. umsdos
20. tmpfs
21. vfat
22. XFS
23. xiafs
##### c. What does the GRUB boot loader therefore have to do to load the kernel?
- When the the firmware gives control to GRUB boot loader, it first look for the .cfg file located in the same directory of the esp partition in the path <code>/boot/efi/EFI/BOOT</code> as seen below.
![](https://i.imgur.com/kcKX9hU.png)
- The config file specifies the path to the real grub.conf file located in <code>/boot/grub/grub.cfg</code>
![](https://i.imgur.com/bMCqnjq.png)
![](https://i.imgur.com/2oyHpJD.png)
- Grub will find and mount the ext4 filesystem, as it can understand this filesystem.
(source: https://unix.stackexchange.com/questions/5518/what-is-the-difference-between-the-following-kernel-makefile-terms-vmlinux-vml)
- Then Grub will parse the config file which will give to GRUB the precise location of the boot image files (vmlinuz and initrd) located in <code>/boot/</code>.
![](https://i.imgur.com/zQb9TKq.png)
![](https://i.imgur.com/sAmID9q.png)

- The GRUB which is also a boot manager, provide a menu for the user to select the and load a specific kernel.
- When the user select the specific kernel, GRUB uses EFI specs to perform its duties before or during kernel loading 
- The duties are :
 1. It must use the proper EFI interfaces to obtain access to the bus specific-resources. i.e , I/O and memory-mapped device registers must be accessed through the proper bus specific I/O calls like those that an EFI driver would perform.
(source :https://www.intel.ru/content/dam/doc/product-specification/efi-v1-10-specification.pdf, page 2-4).
2. While during OS loading , if the OS loader experiences a problem, it sends a service Exit() code call and return control to the UEFI firmware.This exit() code contains both the error code and exit data to be returned.
- If all the GRUB realises all sources are signed from a legitimate sources, it loads the kernel and the hands over control to it using the <code>boot</code> command.
(source : https://www.unix-ninja.com/p/Manually_booting_the_Linux_kernel_from_GRUB)
##### 6. Do you need an OS loader and/or boot loader to load a Linux kernel with UEFI? Explain why or why not.
- Since I am using ubuntu 18.04LTS arm64, I will focus my explanation based on this platform.
- Arm64 systems dont have compressed support of kernel like its counterpart x86 whose kernel and efi stub loader reside in the same container.
- Consequently, the Image itself masquarade as a portable executable(PE) image and the EFI stub is therefore linked into the kernel.
- The location of the EFI stub is <code>arch/arm64/kernel/efi-entry.S</code> and the drivers <code>drivers/firmware/efi/libstub/arm64-stub.c</code>
(source : https://www.kernel.org/doc/Documentation/efi-stub.txt)
- So we can conclude that by using the EFI boot stub it's possible to boot a Linux kernel without the use of a conventional EFI boot loader, such as grub or elilo. Since the EFI boot stub performs the jobs of a boot loader, in a certain sense it *IS* the boot loader.
- The EFI boot stub is enabled with the CONFIG_EFI_STUB kernel option.
- This can be tested or checked using the command <code> cat /boot/config-5.0.0-23-generic | grep EFI_STUB</code> as ssen below.
(source : https://superuser.com/questions/287371/obtain-kernel-config-from-currently-running-linux-system))
![](https://i.imgur.com/u6HyzDv.png)
### In an MBR-based system, the GRUB bootloader is distributed over the disk in multiple parts.
### 7. How many parts (or stages) does GRUB have in such a system, and what is their task?
- During booting, The the BIOS perform basic hardware tests in a process called POST.Then, search , locate execute and hand over control to the GRUB.
- The GRUB since at the level of its design have been splitted into many parts due to the small space where GRUB stage 1 reside(Approx 32KB).
- The above process is generally called stage 1 which just contain the smaller part of GRUB.While the bigger part is found in the filesystem partition.
- The first stage of the bootloader fits into the first sector on disk, its a really small part whose purpose is to load the stage 1.5 code to memory and start its execution.
- Then Grub stage 1  hands over control to Grub 1.5.

- In this stage, the responsibility of Grub 1.5 is to load them in the RAM and passes control to it, since it contains the necessary drivers for reading file systems like ext4. Grub 1.5 is located around LBA1 to LBA63 of the MBR.

- GRUB Stage 1.5 will load the file system drivers and then parse or access <code> /boot/grub/grub.conf</code> file which contains details about kernel path and initrd path.
![](https://i.imgur.com/8lHO8mY.png)
- From this, we go to stage 2 GRUB.
- Stage 2 GRUB have more fucntionalities like ; bootmanager, menu for OS selection, etc.It is located in the filesystem partition /boot/grub/.
- The kernel, VMlinuz is a compressed linux kernel which loads the OS in the memory.

- VMLINUZ stands for vmlinuz = Virtual Memory LINUx gZip = Compressed Linux kernel Executable.

(source:http://independent-software.com/operating-system-development-first-and-second-stage-bootloaders.html)
- On the other hand, the initrd, initial RAM disk is the first root file system mounted and used before the real one is available. It is mostly loaded with the kernel during booting.
- If we decompile the initrd, we will see folders mostly similar to the real file system to enable an initial root file system for the kernel.
![](https://i.imgur.com/5YedJ7v.png)

(source :https://www.gnu.org/software/grub/manual/grub/grub.html#Images)
### 8. Where are the different stages found on the disk?
- Consider the picture below.(Source : www.wikipedia.org)

![](https://i.imgur.com/HEPNE7q.png)

- From the diagram, we realise that the first stage of GRUB reside in the first sector of the bootloader.
- Stage 1.5 reside around sector 1 to sector 62.
- While stage 2 lies in the file system /boot/grub/.








### 9. Describe the entire startup process of Ubuntu 16.04 in the default installation. The subquestions below are leaders to help you along, they must be answered but by no means represent the entire startup process of Ubuntu.

### (a) What is the first process started by the kernel?
- The kernel uses programs like <code>insmod and rmmod</code>  provided to it by the initial root file system initrd to loads and unloads its modules.
- The kernel check the necessary informations about the processor(family, architecture, etc).
- From ubuntu 15.04, 16.04 ,... (upward), the first process started by the kernel is the <code>systemd</code> located in the <code>/bin/systemd</code>directory. 
![](https://i.imgur.com/S0szxcg.png)

- It is considered as the parent process so it has the PID value of 1 as seen below. 
![](https://i.imgur.com/S4ZX32a.png)

- According to wikipedia, The systemd software suite provides fundamental building blocks for a Linux operating system. It includes the systemd "System and Service Manager", an init system used to bootstrap user space and manage user processes. systemd aims to unify service configuration and behavior across Linux distributions. (source : https://en.wikipedia.org/wiki/Systemd)
- Sample systemd running orphan processes.
![](https://i.imgur.com/1Cz9mlA.png)
### b. Where is the configuration kept for the started process?
- The configuration files for systemd is located in the <code> /etc/systemd/system </code>
![](https://i.imgur.com/7EGrkdI.png)

### c. It starts multiple processes. How is the order of execution defined?
- Sytemd define its services as unit files.Usually, what is being manipulated is called a unit.Example: services(.serve), mount points(.mount), devices(.device),sockets(.socket),etc.
![](https://i.imgur.com/aI1S5JV.png)

(source : https://www.slideshare.net/nussbauml/systemd-46731240)
- These files have all dependencies and configs that they require.
- Run levels are described in target units while all processes initiated by the service unit are tagged with the same control group(cgroup). 
- When systemd is intiated, it will activate all units that are dependencies of default.target (also recursively all dependencies of these dependencies).
- Sometimes, default.target is simply another name for graphical.target or multi-user.target, depending on whether the system is configured for a graphical UI or only for a text console. 
- To enforce minimal ordering between the units pulled in, a number of well-known target units are available, as listed on systemd.special(7).
(source : http://manpages.ubuntu.com/manpages/xenial/man7/bootup.7.html)
- The systemd.special(7) are the few units  which are treated specially by systemd.They have special internal semantics and cannot be renamed.
(source: http://manpages.ubuntu.com/manpages/xenial/man7/systemd.special.7.html)

- The following Diagram shows the structural overview of the boot-logic, showing the order of processes pulled in by the systemd manager.

           local-fs-pre.target
                    |
                    v
           (various mounts and   (various swap   (various cryptsetup
            fsck services...)     devices...)        devices...)       (various low-level   (various low-level
                    |                  |                  |             services: udevd,     API VFS mounts:
                    v                  v                  v             tmpfiles, random     mqueue, configfs,
             local-fs.target      swap.target     cryptsetup.target    seed, sysctl, ...)      debugfs, ...)
                    |                  |                  |                    |                    |
                    \__________________|_________________ | ___________________|____________________/
                                                         \|/
                                                          v
                                                   sysinit.target
                                                          |
                     ____________________________________/|\________________________________________
                    /                  |                  |                    |                    \
                    |                  |                  |                    |                    |
                    v                  v                  |                    v                    v
                (various           (various               |                (various          rescue.service
               timers...)          paths...)              |               sockets...)               |
                    |                  |                  |                    |                    v
                    v                  v                  |                    v              rescue.target
              timers.target      paths.target             |             sockets.target
                    |                  |                  |                    |
                    v                  \_________________ | ___________________/
                                                         \|/
                                                          v
                                                    basic.target
                                                          |
                     ____________________________________/|                                 emergency.service
                    /                  |                  |                                         |
                    |                  |                  |                                         v
                    v                  v                  v                                 emergency.target
                display-        (various system    (various system
            manager.service         services           services)
                    |             required for            |
                    |            graphical UIs)           v
                    |                  |           multi-user.target
                    |                  |                  |
                    \_________________ | _________________/
                                      \|/
                                       v
                             graphical.target


(source: http://manpages.ubuntu.com/manpages/xenial/man7/bootup.7.html )
- Global view of the processes can be seen using the command <code>systemd-analyze plot > plot.svg</code>
![](https://i.imgur.com/5X7o93s.png)

![](https://i.imgur.com/973cMii.png)
- Zoomed view of the processes.
![](https://i.imgur.com/KSQQdJw.png)

-  A more summarized version of this chart can be found on the diagram below.We will consider this to explain the next stages.
(https://linoxide.com/linux-how-to/systemd-boot-process/)
![](https://i.imgur.com/QX4CsV7.png)

- Since run levels with init are compatible with targets in systemd, we can compare the two as seen below.
![](https://i.imgur.com/PKbsz6Y.png)
- As seen in my PC, via the command <code>ls -al /lib/systemd/system/runlevel*</code>
![](https://i.imgur.com/pivnvtu.png)

(source: https://www.linuxnix.com/introducing-systemd/)
- Systemd is managed by series of command called as “*ctl” commands to manage services and targets units like tiemedactl, hostnamectl etc.
(source :https://www.linuxnix.com/introducing-systemd/)
### How is the order of processes started
1. The first process(target) executed by systemd is default.target.This is like a shortcut to graphical.target.Graphical.target file is located at /usr/lib/systemd/system/graphical.target path.See the content in the picture below.
![](https://i.imgur.com/hus51Xf.png)
2. This stage means the target process multi-user.target has been invoked and recursively its sub units too found in <code>/etc/systemd/system/multi-user.target.wants</code> directory(see picture below).This target then environment for multiuser support.This stage also enable non root users and firewall related services.
![](https://i.imgur.com/dyhGRMd.png)
- "multi-user.target" passes control to another layer “basic.target”.
![](https://i.imgur.com/Rv3FWkN.png)

3.  "basic.target" unit is the one that starts usual services specially graphical manager service. It uses /etc/systemd/system/basic.target.wants directory to decide which services need to be started
- basic.target passes on control to sysinit.target.
4. "sysinit.target" starts important system services like file System mounting, swap spaces and devices, kernel additional options etc. sysinit.target passes on startup process to local-fs-pre.target.See picture below.
![](https://i.imgur.com/oeJ0s9i.png)
5. The local-fs-pre.target starte no user service.
- It handles low core low level services only.
(Source : https://linoxide.com/linux-how-to/systemd-boot-process/)

### To understand the workings of daemons in Ubuntu, we are going to take a closer look at one aspect of the booting process: networking. Please describe the workings of Ubuntu desktop here (Ubuntu server networking is actually simpler):


- Netplan is a utility for easily configuring networking on a linux system. 
- The network configuration abstraction renderer
- You simply create a YAML description of the required network interfaces and what each should be configured to do. 
(source : https://netplan.io/)
- From this description Netplan will generate all the necessary configuration for your chosen renderer tool.
- We use the command <code>sudo nano /etc/netplan/*.yaml</code>
![](https://i.imgur.com/hMyqG7N.png)

- So netplan renders either NetworkManager or Networkd.In our case we use NetworkManager since we are using Ubuntu desktop.
- In our yaml file located in /etc/netplan/.
- We realise that the renderer is tagged to NetworkManager.
![](https://i.imgur.com/zw8A5r3.png)
- The network management daemon attempts to facilitate intercnnectivity in ubuntu desktop by managing the primary network connection and other network interfaces like ethernet,wifi etc.
- Information about networking here is exported using the D-bus interface to any interested application.
- This daemon makes it easy and automatic that when a user plug in a wired network ,the computer switch to wired and when it is unplugged and connected to wifi, it automatically move to wifi.Most of the times, users don't even notice that their connections have been managed for them.They ma only see interrupted network connectivity.
(source: https://developer.gnome.org/NetworkManager/stable/NetworkManager.html)

- This depends on which initialization subsystems are running either upstart or systemd.
- Since we are running ubuntu 16.04, we are definitely using systemd as our default initialization system which was introduced in versions 15.04 and above releases while the previous versions support upstart as well by default.
![](https://i.imgur.com/OuXpStD.png)
- After a reboot, we start network manager using the : 
<code>sudo systemctl start NetworkManager.service</code>
![](https://i.imgur.com/KNZTQra.png)

- Then we enable restart of the network manager when the system reboots via the command: 
<code>sudo systemctl enable NetworkManager.service</code>
![](https://i.imgur.com/z1pVE6g.png)
(source :https://help.ubuntu.com/community/NetworkManager )
- We usually use netplan for easy configuration of networking on a system via the yaml file.
- Netplan processes the YAML and generate required configurations for NetworkManager, the system's renderer.
- Three target units take the role of $network:
 1. Network.target : this has very limited function as it is only used to indicate that the network management stack is up after it is reached.
 2. network-online.target : It actively waits until the network is "up".
 3. network-pre.target : It is used to arrange other services before any interfaces are configured. Its primary purpose is for usage with firewall services that want to establish a firewall before any network interface is up. 
 (source: https://www.freedesktop.org/wiki/Software/systemd/NetworkTarget/)
![](https://i.imgur.com/uOiW7C7.png)
- If we use better tools to map our network, we will have this : 
![](https://i.imgur.com/O9s3TbK.png)
- We use neato to have this wonderful beautiful interface.
 graph g {  
    overlap = false;
    node [shape=record,height=.1];  
    PC8[label="{{<GigE1>GigE1|<GigE2>GigE2}|{<name>PC8}|{<dvi1>dvi1|<dvi2>dvi2|<dvi3>dvi3|<dvi4>dvi4}}"];  
    PC9[label="{{<GigE1>GigE1|<GigE2>GigE2}|{<name>PC9}|{<dvi1>dvi1|<dvi2>dvi2|<dvi3>dvi3|<dvi4>dvi4}}"];
    C1[label = "{{<dvi1>dvi1}|{<name>C1}}"];  
    C2[label = "{{<dvi1>dvi1}|{<name>C2}}"];  
    C3[label = "{{<dvi1>dvi1}|{<name>C3}}"];  
    C4[label = "{{<dvi1>dvi1}|{<name>C4}}"];  
    D1[label = "{{<dvi1>dvi1}|{<name>D1}}"];  
    D2[label = "{{<dvi1>dvi1}|{<name>D2}}"];  
    "PC8":dvi1 -- "C1":dvi1;  
    "PC8":dvi2 -- "C2":dvi1;  
    "PC8":dvi3 -- "C3":dvi1;  
    "PC8":dvi4 -- "C4":dvi1;  
    "PC9":dvi1 -- "D1":dvi1;  
    "PC9":dvi2 -- "D2":dvi1;  
}
(source : https://stackoverflow.com/questions/1039785/prevent-overlapping-records-using-graphviz-and-neato)
4. docker.services: From the graph we realise that we also have docker.services which enble connectivity between docker containers.
