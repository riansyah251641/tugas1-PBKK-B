# Jarkom-Modul-2-A05-2023

## Penyelesaian

1. Yudistira (Master) akan digunakan sebagai DNS Master, Werkudara sebagai DNS Slave, Arjuna merupakan Load Balancer yang terdiri dari beberapa Web Server yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Buatlah topologi dengan pembagian [sebagai berikut.](https://docs.google.com/spreadsheets/d/1OqwQblR_mXurPI4gEGqUe7v0LSr1yJViGVEzpMEm2e8/edit?usp=sharing) Folder topologi dapat diakses pada [drive berikut](https://drive.google.com/drive/folders/1Ij9J1HdIW4yyPEoDqU1kAwTn_iIxg3gk?usp=sharing)
    
    ![Untitled](img/Untitled.png)
    
    1. Pandudewanata (Server)
        
        ```jsx
        auto eth0
        iface eth0 inet dhcp
        
        auto eth1
        iface eth1 inet static
        	address 192.202.1.1
        	netmask 255.255.255.0
        
        auto eth2
        iface eth2 inet static
        	address 192.202.2.1
        	netmask 255.255.255.0
        
        auto eth3
        iface eth3 inet static
        	address 192.202.3.1
        	netmask 255.255.255.0
        ```
        
    2. Nakula (Client)
        
        ```jsx
        auto eth0
        iface eth0 inet static
        	address 192.202.1.2
        	netmask 255.255.255.0
        	gateway 192.202.1.1
        ```
        
    3. Sadewa (Client)
        
        ```jsx
        auto eth0
        iface eth0 inet static
        	address 192.202.1.3
        	netmask 255.255.255.0
        	gateway 192.202.1.1
        ```
        
    4. Yudistira (Master)
        
        ```jsx
        auto eth0
        iface eth0 inet static
        	address 192.202.1.4
        	netmask 255.255.255.0
        	gateway 192.202.1.1
        ```
        
    5. Werkudara (Slave)
        
        ```jsx
        auto eth0
        iface eth0 inet static
        	address 192.202.1.5
        	netmask 255.255.255.0
        	gateway 192.202.1.1
        ```
        
    6. Arjuna (WebServer)
        
        ```jsx
        auto eth0
        iface eth0 inet static
        	address 192.202.2.2
        	netmask 255.255.255.0
        	gateway 192.202.2.1
        ```
        
    7. Prabukusuma (Worker)
        
        ```jsx
        auto eth0
        iface eth0 inet static
        	address 192.202.3.2
        	netmask 255.255.255.0
        	gateway 192.202.3.1
        ```
        
    8. Abimanyu (WebServer-Worker)
        
        ```jsx
        auto eth0
        iface eth0 inet static
        	address 192.202.3.3
        	netmask 255.255.255.0
        	gateway 192.202.3.1
        ```
        
    9. Wisanggeni (Worker)
        
        ```jsx
        auto eth0
        iface eth0 inet static
        	address 192.202.3.4
        	netmask 255.255.255.0
        	gateway 192.202.3.1
        ```
        
    10. meletakkan `iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.202.0.0/16` pada `/root/.bashrc` pada **Pandudewanata**. Terakhir, tambahkan `echo 'nameserver 192.168.122.1' > /etc/resolv.conf` untuk setiap node  pada `/root/.bashrc`
    - Pada setiap node masukkan bash ini untuk mengetes akses ke internet
        
        ```jsx
        echo '#!/bin/bash
        ping -c 5 google.com' > soal1.sh
        ```
        
    - Jalankan Script
        
        ```jsx
        nano /root/.bashrc
        ./soal1.sh
        ```
        
        ![Untitled](img/Untitled%201.png)
        
2. Buatlah website utama pada node arjuna dengan akses ke **arjuna.yyy.com** dengan alias **www.arjuna.yyy.com** dengan yyy merupakan kode kelompok. Server akan diletakkan di Yudhistira sehingga masuk ke node yudhistira
    
    Jadikan arjuna.A05.com sebagai master
    
    ```
     apt-get update
     apt-get install bind9 -y
    ```
    
    ```jsx
    echo 'zone "arjuna.A05.com" {
            type master;
            file "/etc/bind/jarkom/arjuna.A05.com";
    };' > /etc/bind/named.conf.local
    
    ```
    
    Pada "arjuna" buat folder di dalam etc/bind
    
    ```
    mkdir /etc/bind/jarkom
    ```
    
    Copy file db.local dan ganti nama
    
    ```
    cp /etc/bind/db.local /etc/bind/jarkom/arjuna.A05.com
    ```
    
    Buka file /etc/bind/arjuna/arjuna.A05.com dan tuliskan
    
    ```
    echo "
    ;
    ; BIND data file for local loopback interface
    ;
    \$TTL    604800
    @       IN      SOA     arjuna.A05.com. root.arjuna.A05.com. (
                                  2         ; Serial
                             604800         ; Refresh
                              86400         ; Retry
                            2419200         ; Expire
                             604800 )       ; Negative Cache TTL
    ;
    @       IN      NS      arjuna.A05.com.
    @       IN      A       192.202.1.4      ; IP Yudhistira
    www     IN      CNAME   arjuna.A05.com.   ; Alias
    @       IN      AAAA    ::1
    " > /etc/bind/jarkom/arjuna.A05.com
    
    ```
    
    Lalu restart bind9
    
    ```jsx
    service bind9 restart
    ```
    
    Test di node lain di sini kami menggunakan node Nakula (Client)
    
    ```jsx
    echo "
    nameserver 192.202.1.4 # IP yudhistira
    " > /etc/resolv.conf
    
    ping arjuna.A05.com -c 3
    ```
    
    ![Untitled](img/Untitled%202.png)
    
3. Dengan cara yang sama seperti soal nomor 2, buatlah website utama dengan akses ke **abimanyu.yyy.com** dan alias **www.abimanyu.yyy.com**.
    
    ```jsx
    echo 'zone "arjuna.A05.com" {
            type master;
            file "/etc/bind/jarkom/arjuna.A05.com";
    };
    zone "abimanyu.A05.com" {
            type master;
            file "/etc/bind/jarkom/abimanyu.A05.com";
    };' > /etc/bind/named.conf.local
    
    cp /etc/bind/db.local /etc/bind/jarkom/abimanyu.A05.com
    
    echo "
    ;
    ; BIND data file for local loopback interface
    ;
    \$TTL    604800
    @       IN      SOA     abimanyu.A05.com. root.abimanyu.A05.com. (
                                  3         ; Serial
                             604800         ; Refresh
                              86400         ; Retry
                            2419200         ; Expire
                             604800 )       ; Negative Cache TTL
    ;
    @       IN      NS      abimanyu.A05.com.
    @       IN      A       192.202.1.4      ; IP Yudhistira
    www     IN      CNAME   abimanyu.A05.com.  ; Alias
    @       IN      AAAA    ::1
    " > /etc/bind/jarkom/abimanyu.A05.com
    
    service bind9 restart
    ```
    
    Test di node Nakula (Client)
    
    ```jsx
    echo "
    nameserver 192.202.1.4 # IP yudhistira
    " > /etc/resolv.conf
    
    ping abimanyu.A05.com -c 3
    ```
    
    ![Untitled](img/Untitled%203.png)
    
4. Kemudian, karena terdapat beberapa web yang harus di-deploy, buatlah subdomain **parikesit.abimanyu.A05.com** yang diatur DNS-nya di Yudhistira dan mengarah ke Abimanyu.
    
    cukup menambahkan `parikesit`
    
    ```jsx
    echo "
    ;
    ; BIND data file for local loopback interface
    ;
    \$TTL    604800
    @       IN      SOA     abimanyu.A05.com. root.abimanyu.A05.com. (
                                  2         ; Serial
                             604800         ; Refresh
                              86400         ; Retry
                            2419200         ; Expire
                             604800 )       ; Negative Cache TTL
    ;
    @       IN      NS      abimanyu.A05.com.
    @       IN      A       192.202.1.4       ; IP yudhistira
    www     IN      CNAME   abimanyu.A05.com.   ; Alias
    parikesit    IN      A       192.202.3.3
    www.parikesit    IN      CNAME   parikesit
    @       IN      AAAA    ::1
    " > /etc/bind/jarkom/abimanyu.A05.com
    
    service bind9 restart
    ```
    
    Test di node lain 
    
    ```jsx
    echo "
    nameserver 192.202.1.4 # IP yudhistira
    " > /etc/resolv.conf
    
    ping parikesit.abimanyu.A05.com -c 5
    ping www.parikesit.abimanyu.A05.com -c 5
    ```
    
    ![Untitled](img/Untitled%204.png)
    
5. Buat juga reverse domain untuk domain utama. (*Abimanyu saja yang direverse*)
    
    tambahkan konfigurasi [in-addr.arpa](http://in-addr.arpa) pada fiile named.conf.local, seperti berikut :
    
    ```jsx
    echo 'zone "arjuna.A05.com" {
            type master;
            file "/etc/bind/jarkom/arjuna.A05.com";
    };
    zone "abimanyu.A05.com" {
            type master;
            file "/etc/bind/jarkom/abimanyu.A05.com";
    };
    
    zone "1.202.192.in-addr.arpa" {
        type master;
        file "/etc/bind/jarkom/1.202.192.in-addr.arpa";
    };' > /etc/bind/named.conf.local
    
    cp /etc/bind/db.local /etc/bind/jarkom/1.202.192.in-addr.arpa
    
    echo "
    ;
    ; BIND data file for local loopback interface
    ;
    \$TTL    604800
    @       IN      SOA     abimanyu.A05.com. root.abimanyu.A05.com. (
                                  5         ; Serial
                             604800         ; Refresh
                              86400         ; Retry
                            2419200         ; Expire
                             604800 )       ; Negative Cache TTL
    ;
    1.202.192.in-addr.arpa.       IN      NS      abimanyu.A05.com.
    4 IN PTR abimanyu.A05.com.
    " > /etc/bind/jarkom/1.202.192.in-addr.arpa
    
    service bind9 restart
    ```
    
    Testing di node Nakula (Client)
    
    ```jsx
    echo "
    nameserver 192.168.122.1 # IP public
    " > /etc/resolv.conf
    
    apt-get update
    apt-get install dnsutils
    
    echo "
    nameserver 192.202.1.4 # IP yudhistira
    " > /etc/resolv.conf
    
    host -t PTR 192.202.1.4
    ```
    
    ![Untitled](img/Untitled%205.png)
    
6. Agar dapat tetap dihubungi ketika DNS Server Yudhistira bermasalah, buat juga Werkudara sebagai DNS Slave untuk domain utama.
    - pada Yudistira (Master)
        
        ```jsx
        echo '
        zone "arjuna.A05.com" {
                type master;
                file "/etc/bind/jarkom/arjuna.A05.com";
        };
        
        zone "abimanyu.A05.com" {
                type master;
        				notify yes;
        				also-notify { 192.202.1.5; }; 
                allow-transfer { 192.202.1.5; }; 
                file "/etc/bind/jarkom/abimanyu.A05.com";
        };
        
        zone "1.202.192.in-addr.arpa" {
            type master;
            file "/etc/bind/jarkom/1.202.192.in-addr.arpa";
        };
        ' > /etc/bind/named.conf.local
        
        service bind9 restart
        ```
        
    - pada Werkudara (Slave)
        
        ```jsx
        apt-get update
        apt-get install bind9 -y
        
        echo '
        zone "abimanyu.A05.com" {
            type slave;
            masters { 192.202.1.4; }; 
            file "/var/lib/bind/abimanyu.A05.com";
        };' > /etc/bind/named.conf.local
        
        service bind9 restart
        ```
        
    
    Test `stop` pada Yudistira (Master) dan coba di node lain
    
    ```jsx
    service bind9 stop
    ```
    
    ![Untitled](img/Untitled%206.png)
    
    Coba akses dari node Nakula (Client)
    
    ```jsx
    echo "
    nameserver 192.202.1.4 # IP yudhistira
    nameserver 192.202.1.5 # IP werkudara
    " > /etc/resolv.conf
    
    ping abimanyu.A05.com -c 5
    ```
    
    ![Untitled](img/Untitled%207.png)
    
7. Seperti yang kita tahu karena banyak sekali informasi yang harus diterima, buatlah subdomain khusus untuk perang yaitu **baratayuda.abimanyu.yyy.com** dengan alias **www.baratayuda.abimanyu.yyy.com** yang didelegasikan dari Yudhistira ke Werkudara dengan IP menuju ke Abimanyu dalam folder Baratayuda.
    - pada Yudistira (Master)
        
        ```jsx
        echo "
        ;
        ; BIND data file for local loopback interface
        ;
        \$TTL    604800
        @       IN      SOA     abimanyu.A05.com. root.abimanyu.A05.com. (
                                      2         ; Serial
                                 604800         ; Refresh
                                  86400         ; Retry
                                2419200         ; Expire
                                 604800 )       ; Negative Cache TTL
        ;
        @                       IN      NS      abimanyu.A05.com.
        @                       IN      A       192.202.3.3         ; IP abimanyu
        www                     IN      CNAME   abimanyu.A05.com.   ; Alias
        parikesit               IN      A       192.202.3.3         ; IP abimanyu
        www.parikesit           IN      CNAME   parikesit           ; Alias
        ns1                     IN      A       192.202.1.5         ; IP Werkudara
        baratayuda              IN      NS      ns1
        www.baratayuda          IN      CNAME   baratayuda          ; Alias
        @       IN      AAAA    ::1
        " > /etc/bind/jarkom/abimanyu.A05.com
        
        echo '
        options {
            directory "/var/cache/bind";
        
            // forwarders {
            //      0.0.0.0;
            // };
        
            //dnssec-validation auto;
        
            allow-query{any;};
        
            auth-nxdomain no;    # conform to RFC1035
            listen-on-v6 { any; };
        };
        ' > /etc/bind/named.conf.options
        
        service bind9 restart
        ```
        
    - Pada Werkudara (Slave)
        
        ```jsx
        echo '
        options {
            directory "/var/cache/bind";
        
            // forwarders {
            //      0.0.0.0;
            // };
        
            //dnssec-validation auto;
        
            allow-query{any;};
        
            auth-nxdomain no;    # conform to RFC1035
            listen-on-v6 { any; };
        };
        ' > /etc/bind/named.conf.options
        
        echo '
        zone "baratayuda**.**abimanyu.A05.com" {
            type master;
            file "/etc/bind/baratayuda/baratayuda.abimanyu.A05.com";
        };
        
        zone "abimanyu.A05.com" {
            type slave;
            masters { 192.202.1.4; }; 
            file "/var/lib/bind/abimanyu.A05.com";
        };
        ' > /etc/bind/named.conf.local
        
        mkdir /etc/bind/baratayuda
        
        cp /etc/bind/db.local /etc/bind/baratayuda/baratayuda.abimanyu.A05.com
        
        echo '
        ;
        ; BIND data file for local loopback interface
        ;
        $TTL    604800
        @               IN      SOA     baratayuda.abimanyu.A05.com. root.baratayuda.abimanyu.A05.com. (
        		         3         ; Serial
        		    604800         ; Refresh
        		    86400          ; Retry
        		    2419200        ; Expire
        		    604800 )       ; Negative Cache TTL
        ;
        @               IN      NS      baratayuda.abimanyu.A05.com.
        @               IN      A       192.202.3.3                    ; IP abimanyu
        www             IN      CNAME   baratayuda.abimanyu.A05.com.
        @               IN      AAAA    ::1
        ' > /etc/bind/baratayuda/baratayuda.abimanyu.A05.com
        
        service bind9 restart
        ```
        
    
    Coba akses dari node Nakula (Client)
    
    ```jsx
    echo "
    nameserver 192.202.1.4 # IP werkudara
    nameserver 192.202.1.5 # IP werkudara
    " > /etc/resolv.conf
    
    ping abimanyu.A05.com -c 3
    ping baratayuda.abimanyu.A05.com -c 3
    ping www.baratayuda.abimanyu.A05.com -c 3
    ```
    
    ```jsx
    named-checkzone www.parikesit.abimanyu.A05.com /etc/bind/jarkom/abimanyu.A05.com
    ```
    
    ![Untitled](img/Untitled%208.png)
    
8. Untuk informasi yang lebih spesifik mengenai Ranjapan Baratayuda, buatlah subdomain melalui Werkudara dengan akses **rjp.baratayuda.abimanyu.yyy.com** dengan alias **www.rjp.baratayuda.abimanyu.yyy.com** yang mengarah ke Abimanyu.
    - Yudistira (Master)
        
        ```jsx
        echo "
        ;
        ; BIND data file for local loopback interface
        ;
        \$TTL    604800
        @       IN      SOA     abimanyu.A05.com. root.abimanyu.A05.com. (
                                      2         ; Serial
                                 604800         ; Refresh
                                  86400         ; Retry
                                2419200         ; Expire
                                 604800 )       ; Negative Cache TTL
        ;
        @                       IN      NS      abimanyu.A05.com.
        @                       IN      A       192.202.3.3         ; IP abimanyu
        www                     IN      CNAME   abimanyu.A05.com.   ; Alias
        parikesit               IN      A       192.202.3.3         ; subdomain abimanyu
        www.parikesit           IN      CNAME   parikesit           ; Alias
        ns1                     IN      A       192.202.1.5         ; IP Werkudara
        baratayuda              IN      NS      ns1
        www.baratayuda          IN      CNAME   baratayuda          ; Alias
        www.**rjp**.baratayuda      IN      CNAME   baratayuda          ; Alias
        @                       IN      AAAA    ::1
        " > /etc/bind/jarkom/abimanyu.A05.com
        
        service bind9 restart
        ```
        
    - Werkudara (Slave)
    
    Coba akses dari node Nakula (Client)
    
    ```jsx
    echo "
    nameserver 192.202.1.4 # IP yudhistira
    nameserver 192.202.1.5 # IP werkudara
    
    " > /etc/resolv.conf
    
    ping rjp.baratayuda.abimanyu.A05.com -c 3
    ping www.rjp.baratayuda.abimanyu.A05.com -c 3
    ```
    
    ![Untitled](img/Untitled%209.png)
    
9. Arjuna merupakan suatu Load Balancer Nginx dengan tiga worker (yang juga menggunakan nginx sebagai webserver) yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Lakukan deployment pada masing-masing worker.
    
    lakukan instalasi Nginx Webserver pada setiap node yang terkait
    
    ```jsx
    apt-get update && apt install nginx php php-fpm -y
    
    service nginx start
    service nginx status
    php -v
    ```
    
    buat direktori baru di `/var/www` dengan nama `jarkom`
    
    ```jsx
    mkdir /var/www/jarkom
    ```
    
    buat file index.php dengan isi 
    
    ```jsx
    echo "
    <?php echo 'Halo, Kamu berada di Wisanggeni'; ?>
    " > /var/www/jarkom/index.php
    ```
    
    Buka file konfigurasi **`/etc/nginx/sites-available/`**
    
    ```jsx
    echo "
    server {
    
     	listen 80;
    
     	root /var/www/jarkom;
    
     	index index.php index.html index.htm;
     	server_name _;
    
     	location / {
     			try_files \$uri \$uri/ /index.php?\$query_string;
     	}
    
     	# pass PHP scripts to FastCGI server
     	location ~ \.php$ {
     	include snippets/fastcgi-php.conf;
     	fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
     	}
    
     location ~ /\.ht {
     			deny all;
     	}
    
     	error_log /var/log/nginx/jarkom_error.log;
     	access_log /var/log/nginx/jarkom_access.log;
     }
    " > /etc/nginx/sites-available/jarkom
    ```
    
    Aktifkan konfigurasi
    
    ```jsx
    ln -s /etc/nginx/sites-available/jarkom /etc/nginx/sites-enabled/
    nginx -t
    systemctl restart nginx
    ```
    
    ![Untitled](img/Untitled%2010.png)
    
    Pada Arjuna
    
    ```jsx
    echo "
    nameserver 192.202.1.4 # IP yudhistira
    nameserver 192.202.1.5 # IP werkudara
    nameserver 192.168.122.1 # IP public
    " > /etc/resolv.conf
    
    apt-get update && apt install nginx php php-fpm -y
    apt-get install lynx
    
    service nginx start
    service nginx status
    php -v
    
    echo "
    upstream backend {
        server 192.202.3.2:8001;
        server 192.202.3.3:8002;
        server 192.202.3.4:8003;
    }
    
    server {
        listen 80;
        server_name arjuna.A05.com www.arjuna.A05.com;
    
        location / {
            proxy_pass http://backend;
        }
    }
    " > /etc/nginx/sites-available/default
    
    ln -s /etc/nginx/sites-available/default /etc/nginx/sites-enabled
    service php7.0-fpm start
    service nginx restart
    
    nginx -t
    ```
    
    - Mengkonfigurasi Nginx untuk load balancing:
        - Buat upstream backend yang berisi daftar server backend: `upstream backend { ... }`
        - Buat konfigurasi server untuk listen pada port 80 dan server_name arjuna: `server { ... }`
        - Atur proxy_pass untuk meneruskan permintaan ke backend: `proxy_pass <http://backend>;`
    
    Ketika dicoba di masing masing worker`lynx localhost`  maka muncul seperti ini :
    
    ![Untitled](img/Untitled%2011.png)
    
    ![Untitled](img/Untitled%2012.png)
    
    ![Untitled](img/Untitled%2013.png)
    
10. Kemudian gunakan algoritma **Round Robin** untuk Load Balancer pada **Arjuna**. Gunakan *server_name* pada soal nomor 1. Untuk melakukan pengecekan akses alamat web tersebut kemudian pastikan worker yang digunakan untuk menangani permintaan akan berganti ganti secara acak. Untuk webserver di masing-masing worker wajib berjalan di port 8001-8003. Contoh
    
    - *Prabakusuma:8001*
    
    - *Abimanyu:8002*
    
    - *Wisanggeni:8003*
    
    - Pada Arjuna
        
        ```jsx
        apt-get update && apt install nginx php php-fpm -y
        apt-get install apache2
        
        service nginx start
        service nginx status
        php -v
        
        echo "
        upstream backend {
            server 192.202.3.2:8001;
            server 192.202.3.3:8002;
            server 192.202.3.4:8003;
        }
        
        server {
            listen 80;
            server_name arjuna;
        
            location / {
                proxy_pass http://backend;
            }
        }
        " > /etc/nginx/sites-available/default
        
        ln -s /etc/nginx/sites-available/default /etc/nginx/sites-enabled
        service nginx restart
        nginx -t
        ```
        
    
    Ketika dicoba di node Nakula (Client) `lynx http:/www.arjuna.A05.com` muncul seperti ini :
    
    ![Untitled](img/Untitled%2011.png)
    
    ![Untitled](img/Untitled%2012.png)
    
    ![Untitled](img/Untitled%2013.png)
    
11. Selain menggunakan Nginx, lakukan konfigurasi Apache Web Server pada worker Abimanyu dengan web server **www.abimanyu.yyy.com**. Pertama dibutuhkan web server dengan DocumentRoot pada /var/www/abimanyu.yyy
    - Pada abimanyu
    
    ```jsx
    echo "
    nameserver 192.202.1.4 # IP yudhistira
    nameserver 192.202.1.5 # IP werkudara
    nameserver 192.168.122.1 # IP public
    " > /etc/resolv.conf
    
    apt-get update
    apt-get install apache2 -y
    apt-get install libapache2-mod-php7.0 -y
    apt-get install unzip -y
    apt-get install wget -y
    
    cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/abimanyu.A05.com.conf
    rm /etc/apache2/sites-available/000-default.conf
    
    echo '
    <VirtualHost *:80>
    
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/abimanyu.A05
        ServerName abimanyu.A05.com
        ServerAlias www.abimanyu.A05.com    
    
        #LogLevel info ssl:warn
    
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
    </VirtualHost>
    ' > /etc/apache2/sites-available/abimanyu.A05.com.conf
    
    a2ensite abimanyu.A05.com.conf
    
    mkdir /var/www/abimanyu.A05
    mkdir /var/www/parikesit.abimanyu.A05
    mkdir /var/www/rjp.baratayuda.abimanyu.A05
    
    wget --no-check-certificate 'https://docs.google.com/uc?export=download&id=1a4V23hwK9S7hQEDEcv9FL14UkkrHc-Zc' -O abimanyu.zip
    unzip abimanyu.zip
    mv abimanyu.yyy.com/* /var/www/abimanyu.A05
    
    wget --no-check-certificate 'https://docs.google.com/uc?export=download&id=1LdbYntiYVF_NVNgJis1GLCLPEGyIOreS' -O parikesit.abimanyu.zip
    unzip parikesit.abimanyu.zip
    mv parikesit.abimanyu.yyy.com/* /var/www/parikesit.abimanyu.A05
    
    wget --no-check-certificate 'https://docs.google.com/uc?export=download&id=1pPSP7yIR05JhSFG67RVzgkb-VcW9vQO6' -O rjp.baratayuda.abimanyu.zip
    unzip rjp.baratayuda.abimanyu.zip
    mv rjp.baratayuda.abimanyu.yyy.com/* /var/www/rjp.baratayuda.abimanyu.A05
    
    service apache2 restart
    ```
    
    - Dalam node Nakula (Client)
    
    ```jsx
    echo '
    nameserver 192.168.122.1
    ' > /etc/resolv.conf
    
    apt-get update
    apt-get install lynx -y
    
    echo '
    nameserver 192.202.1.4 #IP yudhistira
    nameserver 192.202.1.5 #IP werkudara
    ' > /etc/resolv.conf
    
    lynx http://www.abimanyu.A05.com
    ```
    
    ![Untitled](img/Untitled%2014.png)
    
12. Setelah itu ubahlah agar url **www.abimanyu.yyy.com/index.php/home** menjadi **www.abimanyu.yyy.com/home.**
    - Abimanyu
        
        ```jsx
        echo '
        <VirtualHost *:80>
        
            ServerAdmin webmaster@localhost
            DocumentRoot /var/www/abimanyu.A05
            ServerName abimanyu.A05.com
            ServerAlias www.abimanyu.A05.com 
        
        		<Directory /var/www/abimanyu.A05/home>
                  Options +Indexes +FollowSymLinks -Multiviews
        					AllowOverride All
        	        Require all granted
        	  </Directory>   
        		Alias "/home" "/var/www/abimanyu.A05/index.php/home"
        
            #LogLevel info ssl:warn
        
            ErrorLog ${APACHE_LOG_DIR}/error.log
            CustomLog ${APACHE_LOG_DIR}/access.log combined
        </VirtualHost>
        ' > /etc/apache2/sites-available/abimanyu.A05.com.conf
        
        service apache2 restart
        ```
        
    - Pada node Nakula (Client)
        
        ```jsx
        lynx abimanyu.A05.com/home
        ```
        
        ![Untitled](img/Untitled%2015.png)
        
13. Selain itu, pada subdomain **www.parikesit.abimanyu.yyy.com,** DocumentRoot disimpan pada /var/www/parikesit.abimanyu.yyy
    - Pada Abimanyu, tambahkan conf DocumenRoot untuk `parikesit.abimanyu.A05`
        
        ```jsx
        echo '
        <VirtualHost *:80>
          ServerAdmin webmaster@localhost
          DocumentRoot /var/www/parikesit.abimanyu.A05
          ServerName parikesit.abimanyu.A05.com
          ServerAlias www.parikesit.abimanyu.A05.com
        
          ErrorLog ${APACHE_LOG_DIR}/error.log
          CustomLog ${APACHE_LOG_DIR}/access.log combined
        </VirtualHost>
        ' > /etc/apache2/sites-available/parikesit.abimanyu.A05.com.conf
        
        a2ensite parikesit.abimanyu.A05.com.conf
        
        service apache2 restart
        ```
        
    - Pada node Nakula (Client)
        
        ```jsx
        lynx abimanyu.A05.com/home
        ```
        
        ![Untitled](img/Untitled%2016.png)
        
14. Pada subdomain tersebut folder /public hanya dapat melakukan *directory listing* sedangkan pada folder /secret tidak dapat diakses *(403 Forbidden)*.
    - Pada Abimanyu, tambahkan conf DocumenRoot option -indexes untuk `/secret` dan +indexes untuk `/public`
        
        ```jsx
        echo '
        <VirtualHost *:80>
          ServerAdmin webmaster@localhost
          DocumentRoot /var/www/parikesit.abimanyu.A05
          ServerName parikesit.abimanyu.A05.com
          ServerAlias www.parikesit.abimanyu.A05.com
        
        	<Directory /var/www/parikesit.abimanyu.A05/public>
                  Options +Indexes
          </Directory>
        
          <Directory /var/www/parikesit.abimanyu.A05/secret>
                  Options -Indexes
          </Directory>
        
        	Alias "/public" "/var/www/parikesit.abimanyu.A05/public"
          Alias "/secret" "/var/www/parikesit.abimanyu.A05/secret"
        
          ErrorLog ${APACHE_LOG_DIR}/error.log
          CustomLog ${APACHE_LOG_DIR}/access.log combined
        </VirtualHost>
        ' > /etc/apache2/sites-available/parikesit.abimanyu.A05.com.conf
        
        a2ensite parikesit.abimanyu.A05.com.conf
        
        service apache2 restart
        ```
        
    - Pada node Nakula (Client)
        
        ```jsx
        lynx parikesit.abimanyu.A05.com/public
        lynx parikesit.abimanyu.A05.com/secret
        ```
        
        ![Untitled](img/Untitled%2017.png)
        
        ![Untitled](img/Untitled%2018.png)
        
15. Buatlah kustomisasi halaman error pada folder /error untuk mengganti error kode pada Apache. Error kode yang perlu diganti adalah 404 Not Found dan 403 Forbidden.
    - Pada file parikesit terdapat folder eror yang telah diberikan `403.html` dan `404.html` , Untuk konfigurasi pengalihan eror, dapat menggunakan ErrorDocument sebagai berikut
        
        ```jsx
        echo '
        <VirtualHost *:80>
          ServerAdmin webmaster@localhost
          DocumentRoot /var/www/parikesit.abimanyu.A05
          ServerName parikesit.abimanyu.A05.com
          ServerAlias www.parikesit.abimanyu.A05.com
        
        	<Directory /var/www/parikesit.abimanyu.A05/public>
                  Options +Indexes
          </Directory>
        
          <Directory /var/www/parikesit.abimanyu.A05/secret>
                  Options -Indexes
          </Directory>
        
        	Alias "/public" "/var/www/parikesit.abimanyu.A05/public"
          Alias "/secret" "/var/www/parikesit.abimanyu.A05/secret"
        
          ErrorDocument 404 /error/404.html
        	ErrorDocument 403 /error/403.html
        
          ErrorLog ${APACHE_LOG_DIR}/error.log
          CustomLog ${APACHE_LOG_DIR}/access.log combined
        </VirtualHost>
        ' > /etc/apache2/sites-available/parikesit.abimanyu.A05.com.conf
        
        a2ensite parikesit.abimanyu.A05.com.conf
        
        service apache2 restart
        ```
        
    - Pada node Nakula (Client)
        
        ```jsx
        lynx parikesit.abimanyu.A05.com/eror
        lynx parikesit.abimanyu.A05.com/secret
        ```
        
        ![Untitled](img/Untitled%2019.png)
        
16. Buatlah suatu konfigurasi virtual host agar file asset **www.parikesit.abimanyu.yyy.com/public/js** menjadi **www.parikesit.abimanyu.yyy.com/js**
    - Pada Abimanyu, tambahkan alias `/js` untuk `"/var/www/parikesit.abimanyu.A05/public/js"`
        
        ```jsx
        echo '
        <VirtualHost *:80>
          ServerAdmin webmaster@localhost
          DocumentRoot /var/www/parikesit.abimanyu.A05
          ServerName parikesit.abimanyu.A05.com
          ServerAlias www.parikesit.abimanyu.A05.com
        
        	<Directory /var/www/parikesit.abimanyu.A05/public>
                  Options +Indexes
          </Directory>
        
          <Directory /var/www/parikesit.abimanyu.A05/secret>
                  Options -Indexes
          </Directory>
        
        	Alias "/public" "/var/www/parikesit.abimanyu.A05/public"
          Alias "/secret" "/var/www/parikesit.abimanyu.A05/secret"
        	Alias "/js" "/var/www/parikesit.abimanyu.A05/public/js"
        
          ErrorDocument 404 /error/404.html
        	ErrorDocument 403 /error/403.html
        
          ErrorLog ${APACHE_LOG_DIR}/error.log
          CustomLog ${APACHE_LOG_DIR}/access.log combined
        </VirtualHost>
        ' > /etc/apache2/sites-available/parikesit.abimanyu.A05.com.conf
        
        a2ensite parikesit.abimanyu.A05.com.conf
        
        service apache2 restart
        ```
        
    - Pada node Nakula (Client)
        
        ```jsx
        lynx parikesit.abimanyu.A05.com/js
        ```
        
        ![Untitled](img/Untitled%2020.png)
        
17. Agar aman, buatlah konfigurasi agar **www.rjp.baratayuda.abimanyu.yyy.com** hanya dapat diakses melalui port 14000 dan 14400.
    - Pada Abimanyu
        
        ```jsx
        echo '
        <VirtualHost *:14000 *:14400>
          ServerAdmin webmaster@localhost
          DocumentRoot /var/www/rjp.baratayuda.abimanyu.A05
          ServerName rjp.baratayuda.abimanyu.A05.com
          ServerAlias www.rjp.baratayuda.abimanyu.A05.com
        
          ErrorDocument 404 /error/404.html
          ErrorDocument 403 /error/403.html
        
          ErrorLog ${APACHE_LOG_DIR}/error.log
          CustomLog ${APACHE_LOG_DIR}/access.log combined
        </VirtualHost>
        ' 
        > /etc/apache2/sites-available/rjp.baratayuda.abimanyu.A05.com.conf
        
        echo '
        # If you just change the port or add more ports here, you will likely also
        # have to change the VirtualHost statement in
        # /etc/apache2/sites-enabled/000-default.conf
        
        Listen 80
        Listen 14000
        Listen 14400
        
        <IfModule ssl_module>
                Listen 443
        </IfModule>
        
        <IfModule mod_gnutls.c>
                Listen 443
        </IfModule>
        
        # vim: syntax=apache ts=4 sw=4 sts=4 sr noet
        ' > /etc/apache2/ports.conf
        
        a2ensite rjp.baratayuda.abimanyu.A05.com.conf
        
        service apache2 restart
        ```
        
    - Pada node Nakula (Client)
        
        ```jsx
        lynx rjp.baratayuda.abimanyu.A05.com:14000
        lynx rjp.baratayuda.abimanyu.A05.com:14400
        lynx rjp.baratayuda.abimanyu.A05.com:10000 //tidak diizinkan
        ```
        
        ![Untitled](img/Untitled%2021.png)
        
        ![Untitled](img/Untitled%2022.png)
        
18. Untuk mengaksesnya buatlah autentikasi username berupa “Wayang” dan password “baratayudayyy” dengan yyy merupakan kode kelompok. Letakkan DocumentRoot pada /var/www/rjp.baratayuda.abimanyu.yyy.
    - Abimanyu, masukkan untuk konfigurasi .htaccess
        
        ```jsx
        echo '
        <VirtualHost *:14000 *:14400>
          ServerAdmin webmaster@localhost
          DocumentRoot /var/www/rjp.baratayuda.abimanyu.A05
          ServerName rjp.baratayuda.abimanyu.A05.com
          ServerAlias www.rjp.baratayuda.abimanyu.A05.com
        
        	<Directory /var/www/rjp.baratayuda.abimanyu.A05>
                  AuthType Basic
                  AuthName "Authentication Required"
                  AuthUserFile /etc/apache2/.htpasswd
                  Require valid-user
          </Directory>
        
          ErrorDocument 404 /error/404.html
          ErrorDocument 403 /error/403.html
        
          ErrorLog ${APACHE_LOG_DIR}/error.log
          CustomLog ${APACHE_LOG_DIR}/access.log combined
        </VirtualHost>
        ' > /etc/apache2/sites-available/rjp.baratayuda.abimanyu.A05.com.conf
        
        htpasswd -c -b /etc/apache2/.htpasswd Wayang baratayudaA05
        
        a2ensite rjp.baratayuda.abimanyu.A05.com.conf
        service apache2 restart
        ```
        
        ![Untitled](img/Untitled%2023.png)
        
    - Pada node Nakula (Client)
        
        ```jsx
        lynx rjp.baratayuda.abimanyu.A05.com:14000
        lynx rjp.baratayuda.abimanyu.A05.com:14400
        ```
        
        ![Untitled](img/Untitled%2024.png)
        
        ![Untitled](img/Untitled%2025.png)
        
        ![Untitled](img/Untitled%2026.png)
        
        ![Untitled](img/Untitled%2027.png)
        
19. Buatlah agar setiap kali mengakses IP dari Abimanyu akan secara otomatis dialihkan ke **[www.abimanyu.yyy.com](http://www.abimanyu.yyy.com/) (alias)**
    - Yudistira (Master), Menambahkan default ketika mengakses IP `abimanyu`
        
        ```jsx
        echo "
        ;
        ; BIND data file for local loopback interface
        ;
        \$TTL    604800
        @       IN      SOA     abimanyu.A05.com. root.abimanyu.A05.com. (
                                      2         ; Serial
                                 604800         ; Refresh
                                  86400         ; Retry
                                2419200         ; Expire
                                 604800 )       ; Negative Cache TTL
        ;
        @                       IN      NS      abimanyu.A05.com.
        @                       IN      A       192.202.3.3         ; IP abimanyu
        www                     IN      CNAME   abimanyu.A05.com.   ; Alias
        parikesit               IN      A       192.202.3.3         ; subdomain abimanyu
        www.parikesit           IN      CNAME   parikesit           ; Alias
        ns1                     IN      A       192.202.1.5         ; IP Werkudara
        baratayuda              IN      NS      ns1
        192.202.3.3             IN      CNAME   www.abimanyu.A05.com.
        @                       IN      AAAA    ::1
        " > /etc/bind/jarkom/abimanyu.A05.com
        ```
        
    - Pada node Nakula (Client)
        
        ```jsx
        lynx 192.202.3.3
        ```
        
        ![Untitled](img/Untitled%2028.png)
        
20. Karena website **www.parikesit.abimanyu.yyy.com** semakin banyak pengunjung dan banyak gambar gambar random, maka ubahlah request gambar yang memiliki substring “abimanyu” akan diarahkan menuju abimanyu.png.
    - Pada Abimanyu
        
        ```jsx
        a2enmod rewrite
        
        echo 'RewriteEngine On
        RewriteCond %{REQUEST_URI} ^/public/images/(.*)abimanyu(.*)
        RewriteCond %{REQUEST_URI} !/public/images/abimanyu.png
        RewriteRule abimanyu http://parikesit.abimanyu.A05.com/public/images/abimanyu.png$1 [L,R=301]
        ' > /var/www/parikesit.abimanyu.A05/.htaccess
        
        echo '
        <VirtualHost *:80>
          ServerAdmin webmaster@localhost
          DocumentRoot /var/www/parikesit.abimanyu.A05
          ServerName parikesit.abimanyu.A05.com
          ServerAlias www.parikesit.abimanyu.A05.com
        
        	<Directory /var/www/parikesit.abimanyu.A05/public>
                  Options +Indexes
          </Directory>
        
          <Directory /var/www/parikesit.abimanyu.A05/secret>
                  Options -Indexes
          </Directory>
        
        	<Directory /var/www/parikesit.abimanyu.A05>
                  Options +FollowSymLinks -Multiviews
                  AllowOverride All
          </Directory>
        	Alias "/public" "/var/www/parikesit.abimanyu.A05/public"
          Alias "/secret" "/var/www/parikesit.abimanyu.A05/secret"
        	Alias "/js" "/var/www/parikesit.abimanyu.A05/public/js"
        
          ErrorDocument 404 /error/404.html
        	ErrorDocument 403 /error/403.html
        
          ErrorLog ${APACHE_LOG_DIR}/error.log
          CustomLog ${APACHE_LOG_DIR}/access.log combined
        </VirtualHost>
        ' > /etc/apache2/sites-available/parikesit.abimanyu.A05.com.conf
        
        a2ensite parikesit.abimanyu.A05.com.conf
        service apache2 restart
        ```
        
    - Pada node Nakula (Client)
        
        ```jsx
        lynx parikesit.abimanyu.A05.com/public/images/abimanyu-student.jpg
        lynx parikesit.abimanyu.A05.com/public/images/notabimanyujustmuseum.177013
        ```
        
        ![Untitled](img/Untitled%2029.png)
