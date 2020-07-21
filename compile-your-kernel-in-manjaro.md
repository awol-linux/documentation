# How to compile your kernel in manjaro
## This is documentation for the video I posted on youtube discussing how to compile the linux kernel on manjaro. I will go over the commands I ran and why I did what I did
### https://youtu.be/_e4VBLoYy8w
1. the first step is to update your system i was using manjaro linux the way to do that on manjaro is and make sure the package groupe base-devel is installed 

``` $ sudo pacman -Syu base-devel```

2. The next thing is to check what version of the kernel you are running that can be done using 

``` $ uname -r ```

3. The next thing is to grab the source tarball you can get from git or [the linux kernel site](https://www.kernel.org).

> Note at this point you dont want to use sudo you will need sudo later but not yet.

4. make a directory to work in I call it src then cd into it
```
$ mkdir src
$ cd src 
```
5. extract the tarball and cd into the new directory
```
$ tar -xvf <path of tarball>
$ cd linux-<kernel-version>
```
6. Next comes configuring the kernel there are three ways to go about this.
    1. you can copy the running config. this is the way i do it in the video it is the easiest way to do it and the the way i do it in the video 
  ``` $ zcat /proc/config.gz > .config ```
  (if there are options in the new kernel that dont exist in the one you are running it will ask you about them to avoid this you can complete the next step and  open a configurator afterwards and it will set all the options to default)  
    2. you can configure it manually   
    I prefer to use ``` $ make menuconfig``` for this   
    you can also use ``` $ make xconfig``` and that would give you gui  
    3. you can install any running modules and thats all
    ``` $ make localmodconfig```
    
> Modules are loadable parts of the kernel i demonstrate this by showing all loaded modules ```lsmod``` unloading psmouse ```sudo modprobe -r psmouse``` if i were to be using a ps2 mouse it wouldve stoped working untill it gets reloaded ```modprobe psmouse``` to set a module in make menuconfig you would have a M in front of it in the brackets instaed of a space or a star.

7. time to compile.  
``` $ make -j9```
> The j after make sets the maximum amount of jobs that make will use. ideally you would want to set this one higher then your current **core** count(not thread count if you don't believe me try it). The reason you want to go one or two over is because it only sets the maximum concurrent jobs it will usaully be using less then what you assign.

8. Compile all the modules  
``` $ make modules -j8```

9. Install the modules  
``` $ sudo make modules_install```  
> From here out you do want to use sudo or be root 

10. Install the kernel
``` $ sudo make install```
> i like to rename the kernel file to include the version ```$ sudo mv /boot/vmlinuz /boot/vmlinuz-<kernel-version>``` 
11. build a initrd image  
``` $ sudo mkinitcpio -k <kernel-version> -g <image-name> ```

12. remake the grub config file
``` $ sudo grub-mkconfig -o /boot/grub/grub.cfg```
> On fedora you would use grub2-mkconfig and the grub config location is /boot/efi/EFI/fedora/grub.cfg if your not sure where it is you can use  
> ```sudo find / -name grub.cfg```

> On majaro the grub-mkconfig doesn't look for kernels that dont have a version appended to the name. if you want you can change that in the files /etc/grub.d/10_linux
> on the line that goes  
> ``` for i in /boot/vmlinuz-* /vmlinuz-* /boot/kernel-* ; do ```  
> and the line that goes  
> ``` for i in /boot/vmlinuz-* /boot/vmlinux-* /vmlinuz-* /vmlinux-* /boot/kernel-* ; do ```  
> you would want to take off the dash after vmlinuz and kernel
> 
> you also have the same thing with the initramfs to change that in the same file find
> ``` for i in "initrd.img-${version}" "initrd-${version}.img" "initrd-${version}.gz" \
>           "initrd-${version}" "initramfs-${version}.img" \
>           "initrd.img-${alt_version}" "initrd-${alt_version}.img" \
>           "initrd-${alt_version}" "initramfs-${alt_version}.img" \
>           "initramfs-genkernel-${version}" \
>           "initramfs-genkernel-${alt_version}" \
>           "initramfs-genkernel-${GENKERNEL_ARCH}-${version}" \
>           "initramfs-genkernel-${GENKERNEL_ARCH}-${alt_version}"; do
> ```
> and add ``` "initramfs.img" \```

13. reboot to new kernel
``` $ sudo reboot ```
While its rebooting after the bios press esc to get to the grub menu. In the grub menu select "advanced options for manjaro linux" and you should see the kernel that compiled. select it and you shoud be good to go
 
