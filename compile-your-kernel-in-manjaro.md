# How to Compile the Linux Kernel in Manjaro
## Documentation for the video I posted on youtube discussing how to compile the linux kernel on Manjaro Linux.
### https://youtu.be/_e4VBLoYy8w
1. Update the system And Install the Base-devel package group.

``` $ sudo pacman -Syu base-devel```

2. Check the running kernel

``` $ uname -r ```

3. Download the source tarball [the linux kernel site](https://www.kernel.org).

> Do not use sudo at this point
4. Create a work directory.
```
$ mkdir src
$ cd src 
```
5. Extract the tarball and cd into it.
```
$ tar -xvf <path of tarball>
$ cd linux-<kernel-version>
```
6. Configuring the new kernel, there are three ways to go about this.
    1. Copy the running config. 
    ``` $ zcat /proc/config.gz > .config ```
    > If there are options in the new kernel that dont exist in the one currently running make will prompt you for each one. To avoid this open a configurator      afterwards and it will set all the options to default.  
    2. you can configure it manually.   
        * TUI ``` $ make menuconfig ``` .
        * GUI ``` $ make xconfig ``` .  
    3. Compile with only current running modules.
    ``` $ make localmodconfig ```
    
7. Compile the kernel.  
``` $ make -j9```
> The j after make sets the maximum amount of jobs that make will use. ideally you would want to set this one higher then your current **core** count(not thread count). The reason to use a few extra jobs is because Make only sets the maximum concurrent jobs however it will usaully be using fewer jobs then what is assigned.

8. Compile the modules  
``` $ make modules -j9```


> From here out you do want to use sudo or be root 
9. Install the modules  
``` $ sudo make modules_install -j9 ```  

10. Install the kernel
``` $ sudo make install```
> If there is a erro

> Optional rename the kernel file to include the version ```$ sudo mv /boot/vmlinuz /boot/vmlinuz-<kernel-version>``` 
11. Build a initrd image  
``` $ sudo mkinitcpio -k <kernel-version> -g <image-name> ```

12. Remake the grub config file
``` $ sudo grub-mkconfig -o /boot/grub/grub.cfg```
> On fedora you would Use ```grub2-mkconfig``` and the grub config location is /boot/efi/EFI/fedora/grub.cfg. In order to locate the grub config file use  
> ```sudo find / -name grub.cfg``` .

> Sometimes the grub-mkconfig doesn't look for kernels that dont have a version appended to the name. To change that edit the script /etc/grub.d/10_linux. 
> Change the line  
> ``` for i in /boot/vmlinuz-* /vmlinuz-* /boot/kernel-* ; do ```  
so it looks like  
> ``` for i in /boot/vmlinuz* /vmlinuz* /boot/kernel* ; do ```  
> and change  
> ``` for i in /boot/vmlinuz-* /boot/vmlinux-* /vmlinuz-* /vmlinux-* /boot/kernel-* ; do ```   
> so it looks like  
> ``` for i in /boot/vmlinuz* /boot/vmlinux* /vmlinuz* /vmlinux* /boot/kernel* ; do ```  
> 
> The same issue applies to the initramfs in the same file add the line ``` "initramfs.img" \ ``` to the pargraph
> ``` for i in "initrd.img-${version}" "initrd-${version}.img" "initrd-${version}.gz" \
>           "initrd-${version}" "initramfs-${version}.img" \
>           "initrd.img-${alt_version}" "initrd-${alt_version}.img" \
>           "initrd-${alt_version}" "initramfs-${alt_version}.img" \
>           "initramfs-genkernel-${version}" \
>           "initramfs-genkernel-${alt_version}" \
>           "initramfs-genkernel-${GENKERNEL_ARCH}-${version}" \
>           "initramfs-genkernel-${GENKERNEL_ARCH}-${alt_version}"; do
> ```
> So it looks like 
>  ``` for i in "initrd.img-${version}" "initrd-${version}.img" "initrd-${version}.gz" \
>           "initrd-${version}" "initramfs-${version}.img" \
>           "initrd.img-${alt_version}" "initrd-${alt_version}.img" \
>           "initrd-${alt_version}" "initramfs-${alt_version}.img" \
>           "initramfs.img" \
>           "initramfs-genkernel-${version}" \
>           "initramfs-genkernel-${alt_version}" \
>           "initramfs-genkernel-${GENKERNEL_ARCH}-${version}" \
>           "initramfs-genkernel-${GENKERNEL_ARCH}-${alt_version}"; do
> ```


13. reboot to new kernel  
``` $ sudo reboot ```  
While its rebooting after the bios press esc to get to the grub menu. In the grub menu select "advanced options for manjaro linux" and select the kernel that you just compiled.
 
