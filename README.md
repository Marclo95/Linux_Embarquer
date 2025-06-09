# Linux_Embarquer
1.3.2 Utilisez un logiciel de liaison série
 
Après "minicom -D /dev/ttyUSB0 -b 115200"
 
Après Ctrl+A puis O > Configurer le port série > F, on ferme le control de port serie.
https://github.com/Marclo95/Linux_Embarquer/blob/6ee75a9345dc0d53b751efba1476e8716d6a47de/Capture%20d'%C3%A9cran%202025-06-06%20172327.png

 
Après redémarrage du SoC, on peut voir les informations pendant cette redémarrage, par exemple, can, Net, bluetooth. Et aussi les résultats des parties, par exemple [ok] started...
A la fin, on voit "Ubuntu 16.04.2 LTS DE10-Standard ttyS0" comme le version et le système du SoC
Après df -h, on voit tous les taille du système, /dev/root est la carte SD, sa taille est 3.0G, les autres sont les systèmes.
https://github.com/Marclo95/Linux_Embarquer/blob/6ee75a9345dc0d53b751efba1476e8716d6a47de/Capture%20d'%C3%A9cran%202025-06-06%20173358.png
 
1.3.3 Configuration réseau
Après ifconfig, on peut voir que les états des réseau, eth0 est par Ethernet.
https://github.com/Marclo95/Linux_Embarquer/blob/d52b02caaefe1e6b1d0b3d5d2ebdd9c6e1427be6/IFCONFIG.png
 
Ensuite, on modifie le fichier /etc/network/interfaces en utilisant les étapes dans le tp :
https://github.com/Marclo95/Linux_Embarquer/blob/6ee75a9345dc0d53b751efba1476e8716d6a47de/Capture%20d'%C3%A9cran%202025-06-07%20001343.png
 
Après reboot une nouvelle fois, on voit l'ip est : 192.168.0.240
Après ping 192.168.0.240, on peut voir la communication entre le pc et le SOC.

 
Dans le fichier sshd_config, on voit :

Pour le chercher,on utilise "/Permit" ensuite "N" pour next cherche. A la fin, on utilise ":q" pour quitter vim sans modification.
 
Après ssh root@192.168.0.240, on voit que la connexion est bonne.

 
Ensuite on quit le minicom par Ctrl+A puis Q :

 
1.4.1 Exploration des dossiers
— Répertoires présent sous la racine

 
— Dans /proc : cpuinfo, ioports, iomem. Utilisez les commandes cat, less ou more pour voir le contenu des fichiers.
1.La commande cat récupère et affiche le contenu d’un fichier dans la console Linux directement. La commande more est un outil puissant pour afficher le contenu d’un fichier tout en filtrant le résultat. La commande less est une extension des capacités de la commande more.
2.Pour affichier le dossier ou le fichier, on a deux façons:
1) Dans le PC, on utilise "cat /proc/cpuinfo"

ioports :
https://github.com/Marclo95/Linux_Embarquer/blob/6ee75a9345dc0d53b751efba1476e8716d6a47de/Capture%20d'%C3%A9cran%202025-06-07%20001619.png
 
iomem :
https://github.com/Marclo95/Linux_Embarquer/blob/6ee75a9345dc0d53b751efba1476e8716d6a47de/Capture%20d'%C3%A9cran%202025-06-07%20001651.png
 
— Le répertoire /sys/class contient des entrées nouvelles (par raport à un PC classique), saurez vous les reconnaître ? En particulier, explorez les répertoires suivants :
— /sys/class/leds/fpga_ledX/
— /proc/ioport
— /proc/iomem
— /proc/device-tree/sopc@0 à comparer avec le fichier iomem
Dans le répertoire /sys/class, on a :
Pour configurer les leds, il faut lier le carte VEEK par minicom, ensuite on peut changer l'état de leds par terminal (0 étaint, 1 allume):
https://github.com/Marclo95/Linux_Embarquer/blob/6ee75a9345dc0d53b751efba1476e8716d6a47de/Capture%20d'%C3%A9cran%202025-06-07%20001706.png
 
1.4.3 Hello world
D'abord, il faut choisir le "Dossiers partagés" dans le bon source par "Configuration" de VM. Ensuite "pwd" pour savoir ou l'on se situe puis "cd src/"(car le point de montage est : src, comme dans la photo ci-dessous), "ls" et on peut voir notre fichier "hello.c", puis en utilisant le command "arm-linux-gnueabihf-gcc hello.c -o hello.o", le hello.c est bien compilé. On peut voir le fichier "hello.o" a été créé automatiquement.

 
Après complication, on utilise "scp hello.o root@192.168.0.240:~/" pour copier le fichier hello.o sur la carte VEEK. Puis "./hello.o" sur terminal pour éxcuter le programme hello.c. Le résultat est :
https://github.com/Marclo95/Linux_Embarquer/blob/6ee75a9345dc0d53b751efba1476e8716d6a47de/Capture%20d'%C3%A9cran%202025-06-07%20001725.png

1.4.4 Accès au matériel
On a fait dans la partie 1.4.1.

1.4.5 Chenillard
On le fait led.c et réalsé correcetment.
 https://github.com/Marclo95/Linux_Embarquer/blob/22c6d4637fceda0ce28c3547f1c9c09e04f27748/chenillard.png
 https://github.com/Marclo95/Linux_Embarquer/blob/22c6d4637fceda0ce28c3547f1c9c09e04f27748/led.png
2 Modules kernel (TP2)
2.1 Accès aux registres

On voit que le LED8 s'allume.
Problèmes et limites :
 
2.2 Compilation de module noyau sur la VM
Arpès installation du "sudo apt install linux-headers-amd64" et "sudo apt install bc". On telecharge le dossier sur Moodle et le renomme comme "module", ensuite dans VM, "ls", "cd module" et "make", puis on trouve que le fichier "hello.ko" a bien créé.
 
On utilise sudo modinfo hello.ko pour voir les informations :

 
On utilise sudo lsmod pour lister les modules chargés :

 
-utilisation de paramètres au chargement du module
Pour l'utilisation de paramètres au chargement du module, on a mis le paramètre = 3 dans le code hello.c, ensuite on a récupéré le paramètre de ce module  = 0. Puis on a utilisé "sudo insmod hello.ko param = 2" pour changer le paramètre à 2. Avec "sudo dmesg", on peut voir le résultat de parmètre et toutes les affichages:

 
-création d’un entrée dans /proc
D'abord, accès au fichiers :

 
Ensuite, on crée les fonction read et write :

 
Oubliez pas ajouter les include et déclaration des variables :
 
Après l'complié, on ecrit "bonjour" dans test par le command "echo "bonjour" > /proc/ensea/test" dans VM, ensuite, on utilise "sudo dmesg" et on voit le message"bonjour".
 
-utilisation d’un timer
On ouvrte le fichier "time_module.c" et réarmer le tiler dans la fonction de gestion pour un timer périodique :

 
Ensuite on compile time_module.c et on voit le timer a été bien périodique.
 
2.3 CrossCompilation de modules noyau
 
2.3.2 Récupéreation de la configuration actuelle du noyau
 
On décompresse le fichier config.gz dans le dossier : /home/ensea/linux-socfpga et renomme le en .config par le commande "gunzip config.gz" et "mv config .config" :
 
Puis on lance les commandes depuis le dossier /home/ensea/linux-socfpga
 
— Quel est le rôle des lignes commençant par export ?
Pour créer les variables CROSS_COMPILE et ARCH ce que on utilise dans prepare et scripts.
 https://github.com/Marclo95/Linux_Embarquer/blob/239954ed17c6299ac9b533608103e9df49cddb0a/Capture%20d'%C3%A9cran%202025-06-07%20001949.png
— Pourquoi le chemin fini par un tiret "-"
C'est pour éviter d'appeler uniquement gcc. En d'autres termes, cela permet d'appeler le chemin complet /usr/bin/arm-linux-gnueabihf avec le gcc.
 
à la fin, on refait pour le timer_module.c dans le dossier module2, et on l'appele par la carte via le terminal :
D'abord, on modifie la 1e ligne dans le file Makefile à "obj-m:=timer_module.o", ensuite on le compile par "make", et on trouve le file "timer_module.ko" dans le dossier, et puis on copie le "timer_module.ko" dans la carte par "scp timer_module.ko root@192.168.0.240" :

 
après le compile, on utilise "insmod timer_module.ko" dans le terminal (avec la carte) et "dmesg" pour tester et voir le résultat de timer:
 
 
