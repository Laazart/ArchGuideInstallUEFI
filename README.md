# Guia para instalar arch linux de forma nativa (UEFI)
>### Verificar conexion y configuraciones iniciales
>>#### Distribucion de su teclado
>>```
>>loadkeys TuDistribucionDeTeclado
>>```
>>#### Verificar la modilidad de arranque
>>```
>>ls /sys/firmware/efi/efivars
>>```
>>#### Verificar conexion a internet
>>En esta guia se estar usando una conexion por cable
>>```
>>ping -c 4 google.com
>>```
>>#### Actualizar reloj del sistema 
>>```
>>timedatctl set-ntp true
>>```
>### Particiones
>>#### Verificar que la unidad este correctamente instalada en su sistema 
>>```
>>lsblk
>>```
>> En mi caso la unidad que utilizare para la instalacion se llama `sda` a lo cual su ruta seria `/dev/sda` 
>>#### Formatear unidad
>>```
>>wipefs --all /dev/sda
>>```
>>#### Crear particiones 
>>Al ejecutar el siguente comando les saldra en pantalla varias opciones, ustedes seleccionaran la que diga "gpt"
>>```
>>cfdisk /dev/sda
>>```
>> En mi caso quedaria de la siguente forma
>>```
>>sda1    512M > Boot
>>sda2    145G > Home
>>sda3    3.5G > Swap
>>```
>>#### Formatear particiones
>>```
>>mkfs.fat -F 32 /dev/sda1
>>```
>>```
>>mkfs.ext4 /dev/sda2
>>```
>>```
>>mkswap /dev/sda3
>>```
>>#### Montar Particiones
>>```
>>swapon /dev/sda3
>>```
>>```
>>mount /dev/sda2 /mnt
>>```
>>```
>>mkdir /mnt/boot
>>```
>>```
>>mount /dev/sda1 /mnt/boot
>>```
>### Instalacion de Paquetes esenciales y configuracion de fstab
>>#### Instalar paquetes esenciales
>>```
>>pacstrap /mnt base base-devel linux linux-firmware neovim sudo networkmanager
>>```
>>#### Configuracion de fstab
>>```
>>genfstab -U /mnt > /mnt/etc/fstab
>>```
>### Configurar nuestro sistema
>>Ahora entraremos en nuestro sistema con el siguente comando
>>```
>>arch-chroot /mnt
>>```
>>#### Zona horaria
>>>Con `ls /usr/share/zoneinfo/` podremos buscar nuestra zona horaria
>>>
>>>Una vez que ya encontremos nuestra configuracion de zona ejecutaremos los siguentes comandos
>>>```
>>>ln -sf /usr/share/zoneinfo/America/santiago /etc/localtime
>>>```
>>>```
>>>hwclock --systohc
>>>```
>>#### Idioma del sistema
>>>Vamos a editar el archivo `/etc/locale.gen` y descomentar las opciones que usaremos, en mi caso seran las siguentes opciones
>>>```
>>>en_US.UTF-8 UTF-8
>>>
>>>es_CL.UTF-8 UTF-8
>>>```
>>>Ahora ejecutaremo el siguente comando.
>>>```
>>>locale-gen
>>>```
>>### Host
>>>#### Hostname
>>>```
>>>echo ElNombreQueElijas > /etc/hostname
>>>```
>>>#### Hosts
>>>Modificaremos el archivo `/etc/hosts` y usaremos esta configuracion inicial:
>>>```
>>>127.0.0.1     localhost
>>>::1           localhost
>>>```
>>### Usuario, grupo y contraseña
>>>Para cambiar la contraseña del usuario root ejecutamos `passwd root `
>>>#### Agregar nuevo usuario y asignarle una contraseña
>>>```
>>>useradd -mG wheel ElNombreQueElijas
>>>
>>>passwd UsuarioCreado
>>>```
>>>Vamos a editar el archivo `/etc/sudoers` y descomentar la siguente opcion `#%wheel ALL=(ALL) ALL` a lo cual nos debera quedar asi:
>>>```
>>>%wheel ALL=(ALL) ALL
>>>```
>>### Bootloader
>>>#### Paquetes necesarios 
>>>Vamos a instalar los siguentes paquetes `grub os-prober efibootmgr`
>>>```
>>>pacman -S grub os-prober efibootmgr
>>>```
>>>#### Instalar grub
>>>```
>>>grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=grub
>>>```
>>>#### Configuracion grub
>>>```
>>>grub-mkconfig -o /boot/grub/grub.cfg
>>>```
>>### Interfaz grafica
>>>#### Paquetes necesarios
>>>```
>>>pacman -S xorg xorg-sever xorg-apps
>>>```
>>>#### Gnome
>>>```
>>>pacman -S gnome
>>>```
>>### Post install
>>Vamos a reiniciar el equipo y quitar el usb con la imagen de arch, si inicia correctamente el sistema es porque la instalacion se hizo correctamente
>>>#### Activar servicios
>>>```
>>>systemctl enable NetworkManager
>>>systemctl start NetworkManager
>>>```
>>>```
>>>systemctl enable gdm
>>>systemctl start gdm.services
>>>```
## Finish
