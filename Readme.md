# "3.9. Элементы безопасности информационных систем"
## 1.
Выполнено
## 2. 
Выполнено
## 3. 
        Systemctl status apache2
        ● apache2.service - The Apache HTTP Server
        Loaded: loaded (/lib/systemd/system/apache2.service; enabled; vendor preset: enabled)
        Active: active (running) since Mon 2022-03-07 08:17:33 UTC; 8min ago
        Main PID: 16820 (apache2)
        CGroup: /system.slice/apache2.service
                ├─16820 /usr/sbin/apache2 -k start
                ├─16821 /usr/sbin/apache2 -k start
                └─16822 /usr/sbin/apache2 -k start

        Mar 07 08:17:33 OrangePi systemd[1]: Starting The Apache HTTP Server...
        Mar 07 08:17:33 OrangePi apachectl[16809]: AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1. Set the 'ServerName' directive globally to suppress this message
        Mar 07 08:17:33 OrangePi systemd[1]: Started The Apache HTTP Server.

Создади сертификат 
        sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/apache-selfsigned.key -out /etc/ssl/certs/apache-selfsigned.crt
Сам конфиг файл

cat /etc/apache2/sites-available/testhome.conf 

        <VirtualHost *:80>
            ServerName testhome
            ServerAlias www.testhome
        #DocumentRoot /var/www/testhome
            RewriteEngine On
            Redirect 301 / https://www.testhome/
            RewriteCond %{HTTPS} off
            RewriteRule (.*) https://%{HTTP_HOST}%{REQUEST_URI} [R=301,L]
        </VirtualHost>
        <VirtualHost *:443>
            ServerName testhome
            ServerAlias www.testhome
            DocumentRoot /var/www/testhome
            SSLEngine on
            SSLCertificateFile ssl/cert.pem
            SSLCertificateKeyFile ssl/cert.key
            #SSLCertificateChainFile ssl/cert.ca-bundle
        </VirtualHost>


Во вложение рис 1 будет, там скрин с браузера
## 4.
        ./testssl.sh -U --sneaky https://www.platon.ru/

        ###########################################################
            testssl.sh       3.1dev from https://testssl.sh/dev/
            (f73bc44 2022-03-02 17:13:41 -- )

            This program is free software. Distribution and
                    modification under GPLv2 permitted.
            USAGE w/o ANY WARRANTY. USE IT AT YOUR OWN RISK!

            Please file bugs @ https://testssl.sh/bugs/

        ###########################################################

        Using "OpenSSL 1.0.2-chacha (1.0.2k-dev)" [~183 ciphers]
        on HP:./bin/openssl.Linux.x86_64
        (built: "Jan 18 17:12:17 2019", platform: "linux-x86_64")


        Start 2022-03-08 09:48:12        -->> 83.169.194.21:443 (www.platon.ru) <<--

        rDNS (83.169.194.21):   --
        Service detected:       HTTP


        Testing vulnerabilities 

        Heartbleed (CVE-2014-0160)                not vulnerable (OK), timed out
        CCS (CVE-2014-0224)                       not vulnerable (OK)
        Ticketbleed (CVE-2016-9244), experiment.  not vulnerable (OK), session IDs were returned but potential memory fragments do not differ
        ROBOT                                     not vulnerable (OK)
        Secure Renegotiation (RFC 5746)           supported (OK)
        Secure Client-Initiated Renegotiation     not vulnerable (OK)
        CRIME, TLS (CVE-2012-4929)                not vulnerable (OK)
        BREACH (CVE-2013-3587)                    no gzip/deflate/compress/br HTTP compression (OK)  - only supplied "/" tested
        POODLE, SSL (CVE-2014-3566)               not vulnerable (OK)
        TLS_FALLBACK_SCSV (RFC 7507)              Downgrade attack prevention supported (OK)
        SWEET32 (CVE-2016-2183, CVE-2016-6329)    VULNERABLE, uses 64 bit block ciphers
        FREAK (CVE-2015-0204)                     not vulnerable (OK)
        DROWN (CVE-2016-0800, CVE-2016-0703)      not vulnerable on this host and port (OK)
                                                make sure you don't use this certificate elsewhere with SSLv2 enabled services
                                                https://censys.io/ipv4?q=012896820AC9D0B9A93CE194ACCB878EAC3A3095E89FB865A767044528656F64 could help you to find out
        LOGJAM (CVE-2015-4000), experimental      not vulnerable (OK): no DH EXPORT ciphers, no DH key detected with <= TLS 1.2
        BEAST (CVE-2011-3389)                     TLS1: AES256-SHA AES128-SHA DES-CBC3-SHA 
                                                VULNERABLE -- but also supports higher protocols  TLSv1.1 TLSv1.2 (likely mitigated)
        LUCKY13 (CVE-2013-0169), experimental     potentially VULNERABLE, uses cipher block chaining (CBC) ciphers with TLS. Check patches
        Winshock (CVE-2014-6321), experimental    not vulnerable (OK) - doesn't seem to be IIS 8.x
        RC4 (CVE-2013-2566, CVE-2015-2808)        no RC4 ciphers detected (OK)


        Done 2022-03-08 09:49:04 [  56s] -->> 83.169.194.21:443 (www.platon.ru) <<--
## 5.
Установка и настройка SSH подключение 
На локальном компе выполнил несколько команд

    ssh-keygen
    Generating public/private rsa key pair.
    Enter file in which to save the key (/home/mikhail/.ssh/id_rsa): 
    Enter passphrase (empty for no passphrase): 
    Enter same passphrase again: 
    Your identification has been saved in /home/mikhail/.ssh/id_rsa
    Your public key has been saved in /home/mikhail/.ssh/id_rsa.pub
    The key fingerprint is:
    SHA256:FNtVat564tXXqY6j4jTBJJxLb3SoU8250RnKBO7Bxkk mikhail@HP
    The key's randomart image is:
    +---[RSA 3072]----+
    |       Eo. ....  |
    |    . * B++.o.   |
    |     = %oO.oo    |
    |    . %.o oo .   |
    |     + *S.  . .  |
    |      o .    . .o|
    |       o    o o.+|
    |      ...  o.+. .|
    |      ......+o   |
    +----[SHA256]-----+
Потом закинул на сервер приват ключ

        ssh-copy-id Mik@192.168.88.239 -p 22640
Далее тестирование

        ssh Mik@192.168.88.239 -p 22640
        Linux OrangePi 5.3.5+ #1 SMP Tue Nov 5 16:13:51 CST 2019 armv7l

        The programs included with the Debian GNU/Linux system are free software;
        the exact distribution terms for each program are described in the
        individual files in /usr/share/doc/*/copyright.

        Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
        permitted by applicable law.
        Last login: Tue Mar  8 07:24:39 2022 from 192.168.88.238
Конект произошёл без пароля
## 6.
Так как у меня изменен стандартный порт для SSH то команда в моём случае выглялит следующим образом

    ssh Mik@Ori -p 22640
    The authenticity of host '[ori]:22640 ([192.168.88.239]:22640)' can't be established.
    ECDSA key fingerprint is SHA256:oxYSG++ncu15xVnHROk0UUj/6SMNHBBey4dZHKJcbtA.
    Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
    Warning: Permanently added '[ori]:22640' (ECDSA) to the list of known hosts.
    Linux OrangePi 5.3.5+ #1 SMP Tue Nov 5 16:13:51 CST 2019 armv7l

    The programs included with the Debian GNU/Linux system are free software;
    the exact distribution terms for each program are described in the
    individual files in /usr/share/doc/*/copyright.

    Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
    permitted by applicable law.
    Last login: Tue Mar  8 07:38:35 2022 from 192.168.88.238
Можно ещё меньше букв
    ssh Mik@O -p 22640
    The authenticity of host '[o]:22640 ([192.168.88.239]:22640)' can't be established.
    ECDSA key fingerprint is SHA256:oxYSG++ncu15xVnHROk0UUj/6SMNHBBey4dZHKJcbtA.
    Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
    Warning: Permanently added '[o]:22640' (ECDSA) to the list of known hosts.
    Linux OrangePi 5.3.5+ #1 SMP Tue Nov 5 16:13:51 CST 2019 armv7l

    The programs included with the Debian GNU/Linux system are free software;
    the exact distribution terms for each program are described in the
    individual files in /usr/share/doc/*/copyright.

    Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
    permitted by applicable law.
    Last login: Tue Mar  8 07:48:29 2022 from 192.168.88.238
Всё делается за счёт изменения файла hosts

        192.168.88.239 Ori O

Можно еще отредачить файл 

    nano ~/.ssh/config
По подобию 

    Host Ori
        HostName 192.168.88.239
        Port 22640
Тогда конект будет происходит без портов

        ssh Mik@Ori
Переименовать файл

        cp id_rsa.pub id_Orirsa.pub
        ls
        config  id_Orirsa.pub  id_rsa  known_hosts
        ssh Mik@Ori
        Linux OrangePi 5.3.5+ #1 SMP Tue Nov 5 16:13:51 CST 2019 armv7l

        The programs included with the Debian GNU/Linux system are free software;
        the exact distribution terms for each program are described in the
        individual files in /usr/share/doc/*/copyright.

        Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
        permitted by applicable law.
        Last login: Tue Mar  8 08:20:09 2022 from 192.168.88.238
Конект произошёл. 
P.S. очень интересное задание. 
## 7.
        sudo tcpdump -i wlx7898e8c1c8b3 -c 100 -w icmp.pcap
        tcpdump: listening on wlx7898e8c1c8b3, link-type EN10MB (Ethernet), capture size 262144 bytes
        100 packets captured
        101 packets received by filter
        0 packets dropped by kernel

Во вложение в папке Git есть скрин Ris 2 и сам файл дампа памяти.
Как читать и на что обращать внимание не понятно. 
## 8.
        sudo nmap scanme.nmap.org
        Starting Nmap 7.80 ( https://nmap.org ) at 2022-03-08 12:04 MSK
        Nmap scan report for scanme.nmap.org (45.33.32.156)
        Host is up (0.19s latency).
        Other addresses for scanme.nmap.org (not scanned): 2600:3c01::f03c:91ff:fe18:bb2f
        Not shown: 993 closed ports
        PORT      STATE    SERVICE
        22/tcp    open     ssh
        80/tcp    open     http
        135/tcp   filtered msrpc
        139/tcp   filtered netbios-ssn
        445/tcp   filtered microsoft-ds
        9929/tcp  open     nping-echo
        31337/tcp open     Elite

        Nmap done: 1 IP address (1 host up) scanned in 10.77 seconds
## 9.
        sudo apt install ufw
Далее
        root@OrangePi:~# ufw allow 22640
        Rule added
        Rule added (v6)
        root@OrangePi:~# ufw allow 80   
        Rule added
        Rule added (v6)
        root@OrangePi:~# ufw allow 443
        Rule added
        Rule added (v6)
        root@OrangePi:~# 
Тестировал сайт на apache во время, заработал только после открытия 443 порта. 