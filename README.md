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
Wij gaan er voor de handleidin vanuit dat de harddisk `/dev/sda` is. Je voert

	#fdisk /dev/sda

Maak drie primaire partities:
1. 2Gb partitie
2. partitie net zo groot als het RAM; voor de swap
3. partitie met de rest van de disk
