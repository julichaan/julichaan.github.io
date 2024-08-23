---
layout: writeup
category: TryHackMe
chall_description: https://tryhackme.com/r/room/allinonemj
points: 160
solves: 2
tags: tryhackme ejptv2
date: 2024-08-23
comments: false
---

### Enumeration and Scanning

Primero de todos hacemos un escaneo de los puertos y sus servicios

![image](https://github.com/user-attachments/assets/197fa6d1-b90b-40b4-9b3c-af2cd5b40bbc)

# Puerto 80 (HTTP Server)

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

### Vulnerability assesment

### Privilege escalation
