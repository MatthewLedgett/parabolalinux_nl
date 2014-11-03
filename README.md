parabolalinux_nl
================

Nederlandse installatie handleiding voor Parabola Linux.

Haal de installatie CD op: http://alfplayer.com/parabola/iso/2014-10-07/parabola-2014.10.07-dual.iso.
Boot van CD en kies de bovenste optie "Boot Parabola GNU/Linux-libre".

Na ongeveer een minuut zie je: 

	root@parabolaiso ~ # 

Voer 

	#fdisk -l

uit om te zien hoe de harddisk wordt genoemd door het systeem; bijvoorbeeld `/dev/sda` of `/dev/sdb`. Voer 

	#free -m 

uit om te zien hoeveel megabyte RAM je hebt.
Wij gaan er voor de handleiding vanuit dat de harddisk `/dev/sda` is. Je voert uit:

	#fdisk /dev/sda

Maak drie primaire partities:

1. 2Gb partitie
2. swap partitie net zo groot als het RAM; vergeet niet het type op 0x82 te zetten.
3. partitie met de rest van de disk

Formatteer de eerste partitie met ext4; dit wordt de `/boot` partitie.

	#mkfs.ext4 /dev/sda1

Maak een gemapte LUKS partitie:

	#cryptsetup -v -y luksFormat /dev/sda3

Voor de duidelijkheid gebruiken wij de wachtwoordzin: `gnu`.

Ontsluit het LUKS volume als `cryptroot`:

	#cryptsetup open /dev/sda3 cryptroot


Nu is ze zichtbaar als `/dev/mapper/cryptroot`. Formatteer haar met ext4:

	#mkfs.ext4 /dev/mapper/cryptroot

Koppel haar nu aan op `/mnt` voor de installatie:

	#mount /dev/mapper/cryptroot /mnt

Koppel daarbinnen `/dev/sda1` aan:

	#mkdir /mnt/boot
	#mount /dev/sda1 /mnt/boot

Neem in `/etc/pacman.d/mirrorlist` als bovenste entry `Server = http://10.0.20.181/parabola/parabola/$repo/os/$arch` op om de lokale mirror van Zeeburgerpad te gebruiken.

Nu is het tijd om `pacstrap` te draaien om een basissysteem te installeren:

	#pacstrap /mnt base grub vim

We schrijven een filesystem tabel met:

	#genfstab -p /mnt >> /mnt/etc/fstab

We gaan het nieuwe systeem in met `chroot`:

	#arch-chroot /mnt

Schrijf de nieuwe hostnaam weg:

	#echo parabola>/etc/hostname

Zet de tijdzone op Amsterdam:

	#ln -sf /usr/share/zoneinfo/Europe/Amsterdam /etc/localtime

Uncommentarieer de locales die je nodig hebt in `/etc/locale.gen` en draai `locale-gen`.

Zet de taal op Nederlands:

	#echo LANG=nl_NL.UTF-8>/etc/locale.conf

Configureer de machine om DHCP te gebruiken om het netwerk in te stellen:

	#systemctl enable dhcpcd

Wijzig `/etc/mkinitcpio.conf` zodat `encrypt` voor `filesystems` in de `HOOKS` regel komt.
Maak nu het initiele ramdisk aan:

	#mkinitcpio -p linux-libre

Zet het rootwachtwoord op `root`:

	#passwd

Wijzig `/etc/crypttab` zodat er een regel voor de swap partitie `/dev/sda2` komt: `swap         /dev/sda2        /dev/urandom            swap,cipher=aes-cbc-essiv:sha256,size=256` in komt. Voorts moet in `/etc/fstab` de volgende regel worden toegevoegd `/dev/mapper/swap		none		swap		sw	0 0`.

Wijzig `/etc/default/grub` zodat de regel met `GRUB_CMDLINE_LINUX` `cryptdevice=/dev/sda3:cryptroot` bevat.  

Installeer GRUB in het MBR:

	#grub-install /dev/sda

En maak de configuratie aan:

	#grub-mkconfig -o /boot/grub/grub.cfg

Verlaat de chroot en ontkoppel je twee mounts:

	#exit
	#umount /mnt/boot
	#umount /mnt

Nu kun je rebooten naar je versleutelde minimale Parabola Linux systeem:

	#reboot
