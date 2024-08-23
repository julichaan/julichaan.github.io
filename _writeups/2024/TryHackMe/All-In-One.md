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

# Enumeration and Scanning
        PORT   STATE SERVICE REASON  VERSION
    21/tcp open  ftp     syn-ack vsftpd 3.0.3
    | ftp-syst: 
    |   STAT: 
    | FTP server status:
    |      Connected to ::ffff:10.21.39.234
    |      Logged in as ftp
    |      TYPE: ASCII
    |      No session bandwidth limit
    |      Session timeout in seconds is 300
    |      Control connection is plain text
    |      Data connections will be plain text
    |      At session startup, client count was 4
    |      vsFTPd 3.0.3 - secure, fast, stable
    |_End of status
    |_ftp-anon: Anonymous FTP login allowed (FTP code 230)
    22/tcp open  ssh     syn-ack OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
    | ssh-hostkey: 
    |   2048 e2:5c:33:22:76:5c:93:66:cd:96:9c:16:6a:b3:17:a4 (RSA)
    | ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDLcG2O5LS7paG07xeOB/4E66h0/DIMR/keWMhbTxlA2cfzaDhYknqxCDdYBc9V3+K7iwduXT9jTFTX0C3NIKsVVYcsLxz6eFX3kUyZjnzxxaURPekEQ0BejITQuJRUz9hghT8IjAnQSTPeA+qBIB7AB+bCD39dgyta5laQcrlo0vebY70Y7FMODJlx4YGgnLce6j+PQjE8dz4oiDmrmBd/BBa9FxLj1bGobjB4CX323sEaXLj9XWkSKbc/49zGX7rhLWcUcy23gHwEHVfPdjkCGPr6oiYj5u6OamBuV/A6hFamq27+hQNh8GgiXSgdgGn/8IZFHZQrnh14WmO8xXW5
    |   256 1b:6a:36:e1:8e:b4:96:5e:c6:ef:0d:91:37:58:59:b6 (ECDSA)
    | ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBF1Ww9ui4NQDHA5l+lumRpLsAXHYNk4lkghej9obWBlOwnV+tIDw4mgmuO1C3U/WXRgn0GrESAnMpi1DSxy8t1k=
    |   256 fb:fa:db:ea:4e:ed:20:2b:91:18:9d:58:a0:6a:50:ec (ED25519)
    |_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIAOG6ExdDNH+xAyzd4w1G4E9sCfiiooQhmebQX6nIcH/
    80/tcp open  http    syn-ack Apache httpd 2.4.29 ((Ubuntu))
    |_http-title: Apache2 Ubuntu Default Page: It works
    | http-methods: 
    |_  Supported Methods: HEAD GET POST OPTIONS
    |_http-server-header: Apache/2.4.29 (Ubuntu)
    Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
