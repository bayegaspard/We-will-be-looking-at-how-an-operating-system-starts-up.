# Describe the entire startup process of Ubuntu 16.04 in the default installation.
- If we consider starting from the booting process of ubuntu as a brief number of steps, we realise that everything starts when we press the power button on our computer.
- From this, the power goes to the ROM(EEPROM=Electrical Erasable programable ROM) which contains the BIOS(or system BIO or ROM BIOS).
- The BIOS perform some integrity check called POST(Power on Self Test) which is the process which check systems harware to know if everything is ok for the next step.
(source : https://www.youtube.com/watch?v=ZplB2v2eMas&feature=youtu.be)
- The configurations for this are stored in the CMOS for further use.
- Sometimes we press F12 or F9 or F2 onour key board depending on the PC/Manufacturer we are having to get to the BIOS.
- The BIOS then look for the bootloader program.
(source : https://www.thegeekstuff.com/2011/02/linux-boot-process/)
- The next step depend on if we are on UEFI mode or Legacy BIOS.

>##### If we are using UEFI firmwares, 
- The firmware locate the GPT partition entry with a specific ID , and name <code>esp(EFI system parition)</code>.
(source: https://askubuntu.com/questions/827491/is-separate-efi-boot-partition-required)
- This partition contain the bootloader which is located in <code> /boot/efi/ubuntu </code>.
- In this directory, we have the bootx64.efi which is an EFI extensible Firmware Interface file.
- We also have the grub.cfg file in this same directory which specifies the location of kernel and contain data on how the boot process should proceed.
(source: https://www.happyassassin.net/2014/01/25/uefi-boot-how-does-that-actually-work-then/)
![](https://i.imgur.com/kRsLvhE.png)

- In UEFI, When the boatloader is loaded and is in control, it uses the only memory given to it by the UEFI enabled firmware. In sumary, It uses EFI specs to perform its duties like: 
- It must use the proper EFI interfaces to obtain access to the bus specific-resources. i.e , I/O and memory-mapped device registers must be accessed through the proper bus specific I/O calls like those that an EFI driver would perform.
(source : https://uefi.org/sites/default/files/resources/UEFI_Spec_2_8_final.pdf)
- The above process is generally called stage 1 and it has as main objective to load the kernel(vmlinuz) and the initrd.
- Then is hands over control to Grub 1.5.
- In this stage, the responsibility of Grub 1.5 is to load them in the RAM and passes control to it, since it contains the necessary drivers for reading file systems. Grub 1.5 is located around LBA1 to LBA63 of the MBR.
- GRUB Stage 1.5 will load the file system drivers and then access /boot/grub/grub.conf file which contains details about kernel path and initrd path.
- The kernel, VMlinuz is a compressed linux kernel which loads the OS in the memory.
- VMLINUZ stands for vmlinuz = Virtual Memory LINUx gZip = Compressed Linux kernel Executable.
- On the other hand, the initrd, initial RAM disk is the first root file system mounted and used before the real one is available. It is mostly loaded with the kernel during booting.
- If we decompile the initrd, we will see folders mostly similar to the real file system to enable an initial root file system for the kernel.
![](https://i.imgur.com/s7YW1oi.png)
 
(source:https://www.ibm.com/developerworks/community/blogs/mhhaque/entry/anatomy_of_the_initrd_and_vmlinuz?lang=en)
- If we see the /boot/ directory closer, we have :
![](https://i.imgur.com/KChSdpV.png)
### (a) What is the first process started by the kernel?
- The kernel uses programs like <code>insmod and rmmod</code>  provided to it by the initial root file system initrd to loads and unloads its modules.
- The kernel check the necessary informations about the processor(family, architecture, etc).
- From ubuntu 15.04, 16.04 ,... (upward), the first process started by the kernel is the <code>systemd</code> located in the <code>sbin/systemd</code>directory. It has the PID value of 1 as seen below. It is considered as the parent process.
![](https://i.imgur.com/S4ZX32a.png)

- According to wikipedia, The systemd software suite provides fundamental building blocks for a Linux operating system. It includes the systemd "System and Service Manager", an init system used to bootstrap user space and manage user processes. systemd aims to unify service configuration and behavior across Linux distributions. (source : https://en.wikipedia.org/wiki/Systemd)
- Sample systemd running orphan processes.
![](https://i.imgur.com/1Cz9mlA.png)
### b. Where is the configuration kept for the started process?
- The configuration files for systemd is located in the <code> /etc/systemd/system </code>
![](https://i.imgur.com/7EGrkdI.png)

### c. It starts multiple processes. How is the order of execution defined?
- Sytemd define its services as unit files.Usually, what is being manipulated is called a unit.Example: services(.serve), mount points(.mount), devices(.device),sockets(.socket), etc.
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
- The more summarized version of this chart can be found on the diagram below.We will consider this to explain the next stages.
(https://linoxide.com/linux-how-to/systemd-boot-process/)
![](https://i.imgur.com/QX4CsV7.png)

- Since run levels with init are compatible with targets in systemd, we can compare the two as seen below.
![](https://i.imgur.com/PKbsz6Y.png)
(source: https://www.linuxnix.com/introducing-systemd/)
- Systemd is managed by series of command called as “*ctl” commands to manage services and targets units like tiemedactl, hostnamectl etc.
(source :https://www.linuxnix.com/introducing-systemd/)
### How is the order of processes started
1. The first process(target) executed by systemd is default.target.This is like a shortcut to graphical.target.Graphical.target file is located at /usr/lib/systemd/system/graphical.target path.See the content in the picture below.
![](https://i.imgur.com/hus51Xf.png)
2. This stage means the target process multi-user.target has been invoked and recursively its sub units too found in <code>/etc/systemd/system/multi-user.target.wants</code> directory(see picture below).This target set then environment for multiuser support.This stage also enable non root users and firewall related services.
![](https://i.imgur.com/dyhGRMd.png)
- "multi-user.target" passes control to another layer “basic.target”.
![](https://i.imgur.com/Rv3FWkN.png)

3.  "basic.target" unit is the one that starts usual services specially graphical manager service. It uses /etc/systemd/system/basic.target.wants directory to decide which services need to be started
- basic.target passes on control to sysinit.target.
4. "sysinit.target" starts important system services like file System mounting, swap spaces and devices, kernel additional options etc. sysinit.target passes on startup process to local-fs.target.See picture below.
![](https://i.imgur.com/oeJ0s9i.png)
5. The local-fs.target starte no user service.
- It handles low core low level services only.
(Source : https://linoxide.com/linux-how-to/systemd-boot-process/)

### To understand the workings of daemons in Ubuntu, we are going to take a closer look at one aspect of the booting process: networking. Please describe the workings of Ubuntu desktop here (Ubuntu server networking is actually simpler):
- Networking here is managed by the "NetworkManager".
- In our yaml file located in /etc/netplan/.We realise that the renderer is NetworkManager.
![](https://i.imgur.com/zw8A5r3.png)
- During booting, systemd starts the <code>system-networkd.service, and the systemd-network</code>