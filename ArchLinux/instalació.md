https://www.youtube.com/watch?v=CX67oR5GjRc

# Afegir un teclat de forma temporal
`loadkeys es`

#Connectar-me a internet
`iwctl`
`device list`
`station device scan`
`station device get-networks`
`station device connect ssid`

# Actualitzar el rellotge del sistema
`timedatectl set-ntp true`

# Particionar el disc
`lsblk`
`fdisk [el disc]`
`g`

# Partició EFI
`ENTER`
`ENTER`
`+200M`
`t`
`1`

# Partició del sistema
`ENTER`
`ENTER`
`ENTER`
`w`

# Formatejant les particions
`mkfs.fat -F32 [partició EFI]`
`mlfs.ext4 [partició del sistema]`

# Montem els discs
`mount [partició del sistema] /mount`
`mkdir /mnt/boot`
`mount [partició EFI] /mnt/boot`

# Actualitzar mirrors
`pacman -Syyy`
`pacman -S reflector`
`reflector -c Spain -a 6 --sort rate --save /etc/pacman.d/mirrorlist`
`pacman -Syyy`

# Instalació base
pacstrap /mnt base linux-firmware nano

# Preparant el sistema
genfstab -U /mnt >> /mnt/etc/fstab
arch-chroot /mnt

# Creant el swap
fallocate -l 2GB /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
nano /etc/fstab    ->    /swapfile   none swap  defaults  0 0

# Zona horària
ln -sf /usr/share/zoneinfo/Europe/Madrid
hwclock --systohc
nano /etc/locale.gen  ->  Descomentar ca_ES.UTF-8 UTF-8
locale-gen
nano /etc/locale.conf  -> Escriure: LANG=ca_ES.UTF-8

# Host Name
nano /etc/hostname
nano /etc/hosts   ->  127.0.0.1 localhost
                      ::1       localhost
                      127.0.0.1 hostname.localdomain hostname
                      
# Crear password root
passwd

# Instalant paquets mínims
pacman -S grub efibootmgr networkmanager network-manager-applet wireless_tools wpa_supplicant dialog os-prober base-devel linux-headers reflector git bluez bluez-utils cups xdg-utils xdg-user-dirs

# Instalar grub
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
grub-mkconfig -o /boot/grub/grub.cfg

# Activar internet i bluetooth
systemctl enable NetworkManager
systemctl enable bluetooth

# Crear usuari
useradd -mG wheel israelps
passwd israelps
EDITOR=nano visudo  -> Descomentar linia %wgeel ALL ALL ALL

# Reinici 
exit
umount -a
reboot

# Conectar-me a internet
nmtui

# Instalar gràfics
sudo pacman -S xf86-video-intel
sudo pacman -S xorg

# Instalar KDE
sudo pacman -S sddm
sudo systemctl enable sddm
sudo pacman -S plasma

# Instalar fonts
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si PKGBUILD
yay -S ttf-ms-fonts

# Activar SSD Trim
sudo systemctl enable fstrim.timer

FIN
