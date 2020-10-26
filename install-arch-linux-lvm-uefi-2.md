1. Update system first not srictly necessarily but always a good idea.  
    `pacman -Syu`

2. Add a user on the machine and give it sudo privlidge.  
    a. add a new user named user  
    `useradd -m user -aG wheel`  
    b. set the password for the user  
    `passwd user`  

3. In order to avoid needing to enter a password change the following line in visudo  
    a. set vim as editor  
    `export editor=vim`  
    b. make sure the following line is commented  
    `%wheel ALL=(ALL) ALL`  
    c. and uncomment the following line  
    `%wheel ALL=(ALL) NOPASSWD: ALL`  


4. In order to use the aur packages need to be compiled  you need to become a user  
    `su - user`

5. Install aur  
    a. copy the source the code into the current directory  
    `git clone https://aur.archlinux.org/yay-git.git`  
    b. cd into the build directory  
    `cd yay-git`  
    c. build the package  
    `makepkg -si`  

6. Install and enable firewall  
    a. install the firewall  
    `pacman -S firewalld`   
    b. enable and start the firewall   
    `systemctl enable firewalld --now`   

7. Install ssh server  
    a. install the ssh server  
    `pacman -S openssh`  
    b. start the ssh server  
    `systemctl enable sshd --now`  

8. Install X11  
    `pacman -S xorg`  
    Press enter to select all  

9. Install lightdm (login manager)  
    a. install login manager  
    `pacman -S lightdm`  
    b. enable login manager  
    `systemctl enable lightdm`  

10. Set default to boot to graphical mode  
    `systemctl set-default graphical.target`  

11. Install desktop enviroment  
    a. install xfce4  
    `pacman -S xfce4`  
    b. install addons (optional)  
    `pacman -S xfce4-goodies`

12. Install flatpak  
    `pacman -S flatpak`

