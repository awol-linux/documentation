Update system  
`pacman -Syu`  

Add sudo user  
`useradd -m user -aG wheel`  
`passwd user`

Become user  
`su - user`

Install aur  
`git clone https://aur.archlinux.org/yay-git.git`  
`cd yay-git`  
`makepkg -si`  

Install and enable firewall  
`pacman -S firewalld`  
`systemctl enable firewalld --now`  

Install ssh server  
`pacman -S openssh`  
`systemctl enable sshd --now`  

Install X11  
`pacman -S xorg`  
Press enter to select all  

Install lightdm (login manager)  
`pacman -S lightdm`  
`systemctl enable lightdm`  

Set default to boot to graphical mode  
`systemctl set-default graphical.target`  

Install xfce4

`pacman -S xfce4`  
`pacman -S xfce4-goodies` #(extra programs)

Install flatpak  
`pacman -S flatpak`

