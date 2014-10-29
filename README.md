parabolalinux_nl
================

Nederlandse installatie handleiding voor Parabola Linux.

Haal de installatie CD op: http://alfplayer.com/parabola/iso/2014-10-07/parabola-2014.10.07-dual.iso
Boot van CD.
Kies de bovenste "Boot Parabola GNU/Linux-libre"

Na ongeveer een minuut zie je: 

	root@parabolaiso ~ # 

Voer 

	#fdisk -l

uit. Om te zien hoe de harddisk wordt genoemd door het systeem; bijvoorbeeld `/dev/sda` of `/dev/sdb`. Voer 

	#free -m 

uit om te zien hoeveel megabyte RAM je hebt.
Wij gaan er voor de handleidin vanuit dat de harddisk `/dev/sda` is. Je voert uit:

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


Nu is ze zichtbaar als `/dev/mapper/cryptroot`.
