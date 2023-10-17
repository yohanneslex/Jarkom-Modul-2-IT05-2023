# Jarkom-Modul-2-IT05-2023

Yohannes Hasahatan Tua Alexandro - 5027211022

Arkan Hendri Abdul Ghani Burhan - 5027211026

## Soal 1
Yudhistira akan digunakan sebagai DNS Master, Werkudara sebagai DNS Slave, Arjuna merupakan Load Balancer yang terdiri dari beberapa Web Server yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Buatlah topologi dengan pembagian untuk kelompok IT05 yaitu topologi 2

## Jawaban

![topologi](https://github.com/yohanneslex/Jarkom-Modul-2-IT05-2023/assets/106576632/28dc74f3-9b52-4a0a-b285-fea98d9fdfe2)



## Soal 2
Buatlah website utama pada node arjuna dengan akses ke arjuna.yyy.com dengan alias www.arjuna.yyy.com dengan yyy merupakan kode kelompok.
## Jawaban

1. Install bind9  dulu pada node yudhistira
2. buat folder jarkom pada /etc/bind/
3. buat file for.arjuna.com pada folder jarkom
4. lalu masukkan bash

```bash
# Untuk setting Zone
# Create the BIND configuration for the "arjuna.it05.com" zone
echo 'zone "arjuna.it05.com" {
    type master;
    allow-transfer { 10.66.3.2; };
    file "/etc/bind/jarkom/for.arjuna.com";
};

zone "abimanyu.it05.com" {
    type master;
    allow-transfer { 10.66.3.2; }; // Masukan IP Water7 tanpa tanda petik
    file "/etc/bind/jarkom/for.abimanyu.com";
};

zone "3.66.10.in-addr.arpa" {
    type master;
    file "/etc/bind/jarkom/rev.abimanyu.com";
};
' > /etc/bind/named.conf.local
```

```bash
# Untuk Setting DNS forward ke arjuna
echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     arjuna.it05.com. root.arjuna.it05.com. (
                            2           ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                         2419200        ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      arjuna.it05.com.
@       IN      A       10.66.3.3
www     IN      CNAME   arjuna.it05.com. 
' > /etc/bind/jarkom/for.arjuna.com
```

## Soal 3
Dengan cara yang sama seperti soal nomor 2, buatlah website utama dengan akses ke abimanyu.yyy.com dan alias www.abimanyu.yyy.com.

## Jawaban
buat file for.abimanyu.com pada folder jarkom lalu masukkan kode berikut ke node yudhistira lagi untuk membuat dns abimanyu

```bash
echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.it05.com. root.abimanyu.it05.com. (
                            2           ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                         2419200        ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      abimanyu.it05.com.
@       IN      A       10.66.3.4
www     IN      CNAME   abimanyu.it05.com.
parikesit   IN  A       10.66.3.4
ns1     IN      A       10.66.3.4
baratayuda  IN  NS      ns1
@       IN      AAAA    ::1
' > /etc/bind/jarkom/for.abimanyu.com
```



## Soal 4
Kemudian, karena terdapat beberapa web yang harus di-deploy, buatlah subdomain parikesit.abimanyu.yyy.com yang diatur DNS-nya di Yudhistira dan mengarah ke Abimanyu.

## Jawaban
Buat file pada folder jarkom dengan nama for.abimanyu.com sebagai forward, dan kode sama seperti sebleumnya dimana sudah menjadi 1 untuk menambahkan domain dan sub domainnya

```bash
echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.it05.com. root.abimanyu.it05.com. (
                            2           ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                         2419200        ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      abimanyu.it05.com.
@       IN      A       10.66.3.4
www     IN      CNAME   abimanyu.it05.com.
parikesit   IN  A       10.66.3.4
ns1     IN      A       10.66.3.4
baratayuda  IN  NS      ns1
@       IN      AAAA    ::1
' > /etc/bind/jarkom/for.abimanyu.com
```



## Soal 5
Buat juga reverse domain untuk domain utama. (Abimanyu saja yang direverse)

## Jawaban
Buat file pada folder jarkom dengan nama rev.abimanyu.com sebagai reverse, lalu masukkan kode berikut

```bash
echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.it05.com. root.abimanyu.it05.com. (
                            2           ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                         2419200        ; Expire
                         604800 )       ; Negative Cache TTL
;
3.66.10.in-addr.arpa.        IN      NS      abimanyu.it05.com.
4                            IN      PTR     abimanyu.it05.com.
' > /etc/bind/jarkom/rev.abimanyu.com
```

lalu masukkan bash berikut juga


```bash
echo "
options {
	directory \"/var/cache/bind\";

	// If there is a firewall between you and nameservers you want
	// to talk to, you may need to fix the firewall to allow multiple
	// ports to talk.  See http://www.kb.cert.org/vuls/id/800113

	// If your ISP provided one or more IP addresses for stable 
	// nameservers, you probably want to use them as forwarders.  
	// Uncomment the following block, and insert the addresses replacing 
	// the all-0's placeholder.

	forwarders {
		192.168.122.1;
	};

	//========================================================================
	// If BIND logs error messages about the root key being expired,
	// you will need to update your keys.  See https://www.isc.org/bind-keys
	//========================================================================
	//dnssec-validation auto;
	allow-query{any;};


	listen-on-v6 { any; };
};" > /etc/bind/named.conf.options
```


## Soal 6
Agar dapat tetap dihubungi ketika DNS Server Yudhistira bermasalah, buat juga Werkudara sebagai DNS Slave untuk domain utama.

## Jawaban
1. install bind9 di node werkudara
2. lalu masukkan bash berikut

```bash
echo 'zone "arjuna.it05.com" {
    type slave;
    masters { 10.66.2.2; }; // Masukan IP EniesLobby tanpa tanda petik
    file "/var/lib/bind/arjuna.it05.com";
};

zone "abimanyu.it05.com" {
    type slave;
    masters { 10.66.2.2; }; // Masukan IP EniesLobby tanpa tanda petik
    file "/var/lib/bind/abimanyu.it05.com";
};

zone "baratayuda.abimanyu.it05.com" {
    type master;
    file "/etc/bind/delegasi/baratayuda.abimanyu.it05.com";
};

zone "rjp.baratayuda.abimanyu.it05.com"{
	type master;
	file "/etc/bind/jarkom/for.rjp.com";
};
' > /etc/bind/named.conf.local
```


## Soal 7
Seperti yang kita tahu karena banyak sekali informasi yang harus diterima, buatlah subdomain khusus untuk perang yaitu baratayuda.abimanyu.yyy.com dengan alias www.baratayuda.abimanyu.yyy.com yang didelegasikan dari Yudhistira ke Werkudara dengan IP menuju ke Abimanyu dalam folder Baratayuda.

## Jawaban
buat folder delegasi pada folder /etc/bind/ lalu buat file bernama baratayuda.abimanyu.it05.com lalu masukkan bash berikut

```bash
echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     baratayuda.abimanyu.it05.com. root.baratayuda.abimanyu.it05.com. (
                            2           ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                         2419200        ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      baratayuda.abimanyu.it05.com.
@       IN      A       10.66.3.4
www     IN      A       10.66.3.4
' > /etc/bind/delegasi/baratayuda.abimanyu.it05.com
```

## Soal 8
Untuk informasi yang lebih spesifik mengenai Ranjapan Baratayuda, buatlah subdomain melalui Werkudara dengan akses rjp.baratayuda.abimanyu.yyy.com dengan alias www.rjp.baratayuda.abimanyu.yyy.com yang mengarah ke Abimanyu.

## Jawaban
buat folder jarkom pada folder /etc/bind/ lalu buat file bernama for.rjp.com lalu masukkan bash berikut

```bash
echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     rjp.baratayuda.abimanyu.it05.com. root.rjp.baratayuda.abimanyu.it05.com. (
                            2           ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                         2419200        ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      rjp.baratayuda.abimanyu.it05.com.
@       IN      A       10.66.3.4
www     IN      CNAME   rjp.baratayuda.abimanyu.it05.com.
@		IN		AAAA	::1
' > /etc/bind/jarkom/for.rjp.com
```

lalu masukkan bash berikut juga

```bash
echo "
options {
	directory \"/var/cache/bind\";

	// If there is a firewall between you and nameservers you want
	// to talk to, you may need to fix the firewall to allow multiple
	// ports to talk.  See http://www.kb.cert.org/vuls/id/800113

	// If your ISP provided one or more IP addresses for stable 
	// nameservers, you probably want to use them as forwarders.  
	// Uncomment the following block, and insert the addresses replacing 
	// the all-0's placeholder.

	forwarders {
		192.168.122.1;
	};

	//========================================================================
	// If BIND logs error messages about the root key being expired,
	// you will need to update your keys.  See https://www.isc.org/bind-keys
	//========================================================================
	//dnssec-validation auto;
	allow-query{any;};


	listen-on-v6 { any; };
};" > /etc/bind/named.conf.options
```

## Soal 9
Arjuna merupakan suatu Load Balancer Nginx dengan tiga worker (yang juga menggunakan nginx sebagai webserver) yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Lakukan deployment pada masing-masing worker.

## Jawaban
1. install nginx pada node arjuna
2. masukkan bash berikut
```bash
echo '
upstream backend {
    server 10.66.3.4:8001;
    server 10.66.3.5:8002;
    server 10.66.3.6:8003;
}

server {
    listen 80;
    
    #root /var/www/arjuna.it05.com
    server_name arjuna.it05.com www.arjuna.it05.com;

    location / {
        proxy_pass http://backend;
    }

    location /arjuno/{
        alias /var/www/arjuna.it05.com;
        index index.php;
    }

}' > /etc/nginx/sites-available/default
```
3. start nginx


## Soal 10
Kemudian gunakan algoritma Round Robin untuk Load Balancer pada Arjuna. Gunakan server_name pada soal nomor 1. Untuk melakukan pengecekan akses alamat web tersebut kemudian pastikan worker yang digunakan untuk menangani permintaan akan berganti ganti secara acak. Untuk webserver di masing-masing worker wajib berjalan di port 8001-8003. Contoh :

    - Prabakusuma:8001
    - Abimanyu:8002
    - Wisanggeni:8003

## Jawaban
Pada node Abimanyu
```bash
gg='server {

 	listen 8002;

 	root /var/www/arjuna.it05.com;

 	index index.php index.html index.htm;
 	server_name _;

 	location / {
 			try_files $uri $uri/ /index.php?$query_string;
 	}

 	# pass PHP scripts to FastCGI server
 	location ~ \.php$ {
 	include snippets/fastcgi-php.conf;
 	fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;#prabukusuma&wisanggeni 7.0
 	}

 location ~ /\.ht {
 			deny all;
 	}

 	error_log /var/log/nginx/jarkom_error.log;
 	access_log /var/log/nginx/jarkom_access.log;
 }'


echo "$gg" > /etc/nginx/sites-available/jarkom
service nginx restart
```
Pada node Arjuna
```bash
apt-get update
apt-get install bind9 nginx

ff='upstream myweb  {
        server 10.66.3.4:8002;
        server 10.66.3.5:8001;
        server 10.66.3.6:8003;
 }

 server {
        listen 80;
        server_name arjuna.E06.com;

        location / {
        proxy_pass http://myweb;
        }
 }'

echo "$ff" > /etc/nginx/sites-available/lb-arjuna
ln -s /etc/nginx/sites-available/lb-arjuna /etc/nginx/sites-enabled
service nginx restart
```
Pada node Prabukusuma
```bash
gg='server {

 	listen 8001;

 	root /var/www/arjuna.it05.com;

 	index index.php index.html index.htm;
 	server_name _;

 	location / {
 			try_files $uri $uri/ /index.php?$query_string;
 	}

 	# pass PHP scripts to FastCGI server
 	location ~ \.php$ {
 	include snippets/fastcgi-php.conf;
 	fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;#prabukusuma&wisanggeni 7.0
 	}

 location ~ /\.ht {
 			deny all;
 	}

 	error_log /var/log/nginx/jarkom_error.log;
 	access_log /var/log/nginx/jarkom_access.log;
 }'


echo "$gg" > /etc/nginx/sites-available/jarkom
service nginx restart
```
## Soal 11
Selain menggunakan Nginx, lakukan konfigurasi Apache Web Server pada worker Abimanyu dengan web server www.abimanyu.yyy.com. Pertama dibutuhkan web server dengan DocumentRoot pada /var/www/abimanyu.yyy

## Jawaban
Pada node Abimanyu
``` bash
apt-get install apache2
service apache2 start

git config --global http.sslVerify false
git clone https://github.com/yohanneslex/Jarkom-Modul-2-IT05

cp -r /Jarkom-Modul-2-IT05/Resource/abimanyu.yyy.com/abimanyu.yyy.com /var/www/abimanyu.IT05.com

gg='<VirtualHost *:80>

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/abimanyu.IT05.com
        ServerName abimanyu.IT05.com
        ServerAlias www.abimanyu.IT05.com

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined


</VirtualHost>
'


echo "$gg" > /etc/apache2/sites-available/000-default.conf

apt-get install libapache2-mod-php7.0
service apache2 restart
```

## Soal 12
Setelah itu ubahlah agar url www.abimanyu.yyy.com/index.php/home menjadi www.abimanyu.yyy.com/home.

## Jawaban
Pada node Abimanyu
```bash
gg='<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/abimanyu.IT05.com
        ServerName abimanyu.IT05.com
        ServerAlias www.abimanyu.IT05.com
        <Directory /var/www/abimanyu.IT05.com/index.php/home>
                Options +Indexes
        </Directory>

        Alias "/home" "/var/www/abimanyu.IT05.com/index.php/home"

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
'


echo "$gg" > /etc/apache2/sites-available/000-default.conf

service apache2 restart
```

## Soal 13
Selain itu, pada subdomain www.parikesit.abimanyu.yyy.com, DocumentRoot disimpan pada /var/www/parikesit.abimanyu.yyy

## Jawaban
Pada node Abimanyu
```bash
apt-get install apache2
service apache2 start

git config --global http.sslVerify false
git clone https://github.com/yohanneslex/Jarkom-Modul-2-IT05


cp -r /Jarkom-Modul-2-IT05/Resource/parikesit.abimanyu.yyy.com/parikesit.abimanyu.yyy.com /var/www/parikesit.abimanyu.IT05

mkdir /var/www/parikesit.abimanyu.IT05/secret

gg='<VirtualHost *:80>

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/parikesit.abimanyu.IT05
        ServerName parikesit.abimanyu.IT05.com
        ServerAlias www.parikesit.abimanyu.IT05.com

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined


</VirtualHost>
'

echo "$gg" >> /etc/apache2/sites-available/000-default.conf
apt-get install libapache2-mod-php7.0
service apache2 restart
```

## Soal 14
Pada subdomain tersebut folder /public hanya dapat melakukan directory listing sedangkan pada folder /secret tidak dapat diakses (403 Forbidden).

## Jawaban
Pada node Abimanyu
```bash
gg='<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/abimanyu.IT05.com
        ServerName abimanyu.IT05.com
        ServerAlias www.abimanyu.IT05.com
        <Directory /var/www/abimanyu.IT05.com/index.php/home>
                Options +Indexes
        </Directory>

        Alias "/home" "/var/www/abimanyu.IT05.com/index.php/home"

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
<VirtualHost *:80>

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/parikesit.abimanyu.IT05
        ServerName parikesit.abimanyu.IT05.com
        ServerAlias www.parikesit.abimanyu.IT05.com
        <Directory /var/www/parikesit.abimanyu.IT05/secret>
                Deny From All
        </Directory>
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
'


echo "$gg" > /etc/apache2/sites-available/000-default.conf
service apache2 restart
```

## Soal 15
Buatlah kustomisasi halaman error pada folder /error untuk mengganti error kode pada Apache. Error kode yang perlu diganti adalah 404 Not Found dan 403 Forbidden.

## Jawaban
Pada node Abimanyu
```bash
gg='<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/abimanyu.IT05.com
        ServerName abimanyu.IT05.com
        ServerAlias www.abimanyu.IT05.com
        <Directory /var/www/abimanyu.IT05.com/index.php/home>
                Options +Indexes
        </Directory>

        Alias "/home" "/var/www/abimanyu.IT05.com/index.php/home"

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
<VirtualHost *:80>

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/parikesit.abimanyu.IT05
        ServerName parikesit.abimanyu.IT05.com
        ServerAlias www.parikesit.abimanyu.IT05.com
        <Directory /var/www/parikesit.abimanyu.IT05/secret>
                Deny From All
        </Directory>
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
'


echo "$gg" > /etc/apache2/sites-available/000-default.conf
service apache2 restart

```

## Soal 16
Buatlah suatu konfigurasi virtual host agar file asset www.parikesit.abimanyu.yyy.com/public/js menjadi 
www.parikesit.abimanyu.yyy.com/js 

## Jawaban
Pada node Abimanyu
```bash
gg='<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/abimanyu.IT05.com
        ServerName abimanyu.IT05.com
        ServerAlias www.abimanyu.IT05.com
        <Directory /var/www/abimanyu.IT05.com/index.php/home>
                Options +Indexes
        </Directory>

        Alias "/home" "/var/www/abimanyu.IT05.com/index.php/home"

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/parikesit.abimanyu.IT05
        ServerName parikesit.abimanyu.IT05.com
        ServerAlias www.parikesit.abimanyu.IT05.com
        <Directory /var/www/parikesit.abimanyu.IT05/secret>
                Deny From All
        </Directory>
        Alias "/js" "/var/www/parikesit.abimanyu.IT05/public/js"
        ErrorDocument 403 /error/403.html
        ErrorDocument 404 /error/404.html
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
'


echo "$gg" > /etc/apache2/sites-available/000-default.conf
service apache2 restart
```

## Soal 17
Agar aman, buatlah konfigurasi agar www.rjp.baratayuda.abimanyu.yyy.com hanya dapat diakses melalui port 14000 dan 14400.

## Jawaban
Pada node Abimanyu
```bash
apt-get install apache2
service apache2 start

git config --global http.sslVerify false
git clone https://github.com/yohanneslex/Jarkom-Modul-2-IT05


cp -r /Jarkom-Modul-2-IT05/Resource/rjp.baratayuda.abimanyu.yyy.com/rjp.baratayuda.abimanyu.yyy.com/ /var/www/rjp.baratayuda.abimanyu.IT05

gg='<VirtualHost *:14000 *:14400>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/rjp.baratayuda.abimanyu.IT05
        ServerName rjp.baratayuda.abimanyu.IT05.com
        ServerAlias www.rjp.baratayuda.abimanyu.IT05.com

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
'

echo "$gg" >> /etc/apache2/sites-available/000-default.conf

aa='
Listen 80
Listen 14000
Listen 14400

<IfModule ssl_module>
        Listen 443
</IfModule>

<IfModule mod_gnutls.c>
        Listen 443
</IfModule>
'
echo "$aa" > /etc/apache2/ports.conf


service apache2 restart
```

## Soal 18
Untuk mengaksesnya buatlah autentikasi username berupa “Wayang” dan password “baratayudayyy” dengan yyy merupakan kode kelompok. Letakkan DocumentRoot pada /var/www/rjp.baratayuda.abimanyu.yyy.

## Jawaban
Pada node Abimanyu
```bash
mkdir /etc/apache2/passwd
htpasswd -c -b /etc/apache2/passwd/password Wayang baratayudaIT05

gg='<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/abimanyu.IT05.com
        ServerName abimanyu.IT05.com
        ServerAlias www.abimanyu.IT05.com
        <Directory /var/www/abimanyu.IT05.com/index.php/home>
                Options +Indexes
        </Directory>

        Alias "/home" "/var/www/abimanyu.IT05.com/index.php/home"

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/parikesit.abimanyu.IT05
        ServerName parikesit.abimanyu.IT05.com
        ServerAlias www.parikesit.abimanyu.IT05.com
        <Directory /var/www/parikesit.abimanyu.IT05/secret>
                Deny From All
        </Directory>
        Alias "/js" "/var/www/parikesit.abimanyu.IT05/public/js"
        ErrorDocument 403 /error/403.html
        ErrorDocument 404 /error/404.html
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
<VirtualHost *:14000 *:14400>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/rjp.baratayuda.abimanyu.IT05
        ServerName rjp.baratayuda.abimanyu.IT05.com
        ServerAlias www.rjp.baratayuda.abimanyu.IT05.com
	<Directory /var/www/rjp.baratayuda.abimanyu.IT05>
                AuthType Basic
		AuthName "Restricted Files"
		# (Following line optional)
		AuthBasicProvider file
		AuthUserFile "/etc/apache2/passwd/password"
		Require user Wayang
        </Directory>
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
'

echo "$gg" >> /etc/apache2/sites-available/000-default.conf
service apache2 restart
```

## Soal 19
Buatlah agar setiap kali mengakses IP dari Abimanyu akan secara otomatis dialihkan ke www.abimanyu.yyy.com (alias)

## Jawaban
```bash
mkdir /etc/apache2/passwd
htpasswd -c -b /etc/apache2/passwd/password Wayang baratayudaIT05

gg='<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/abimanyu.IT05.com
        ServerName abimanyu.IT05.com
        ServerAlias www.abimanyu.IT05.com
        <Directory /var/www/abimanyu.IT05.com/index.php/home>
                Options +Indexes
        </Directory>

        Alias "/home" "/var/www/abimanyu.IT05.com/index.php/home"

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/parikesit.abimanyu.IT05
        ServerName parikesit.abimanyu.IT05.com
        ServerAlias www.parikesit.abimanyu.IT05.com
        <Directory /var/www/parikesit.abimanyu.IT05/secret>
                Deny From All
        </Directory>
        Alias "/js" "/var/www/parikesit.abimanyu.IT05/public/js"
        ErrorDocument 403 /error/403.html
        ErrorDocument 404 /error/404.html
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
<VirtualHost *:14000 *:14400>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/rjp.baratayuda.abimanyu.IT05
        ServerName rjp.baratayuda.abimanyu.IT05.com
        ServerAlias www.rjp.baratayuda.abimanyu.IT05.com
	<Directory /var/www/rjp.baratayuda.abimanyu.IT05>
                AuthType Basic
		AuthName "Restricted Files"
		# (Following line optional)
		AuthBasicProvider file
		AuthUserFile "/etc/apache2/passwd/password"
		Require user Wayang
        </Directory>
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
'

echo "$gg" >> /etc/apache2/sites-available/000-default.conf
service apache2 restart
```


## Soal 20
Karena website www.parikesit.abimanyu.yyy.com semakin banyak pengunjung dan banyak gambar gambar random, maka ubahlah request gambar yang memiliki substring “abimanyu” akan diarahkan menuju abimanyu.png.

## Jawaban
```bash
a2enmod rewrite

gg='<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/abimanyu.IT05.com
        ServerName abimanyu.IT05.com
        ServerAlias www.abimanyu.IT05.com
        <Directory /var/www/abimanyu.IT05.com/index.php/home>
                Options +Indexes
        </Directory>
        <Directory /var/www>
        RewriteEngine on
        RewriteCond %{HTTP_HOST} ^10\.66\.3\.4$
        RewriteRule /.* http://www.abimanyu.IT05.com [R]
        </Directory>
        Alias "/home" "/var/www/abimanyu.IT05.com/index.php/home"

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/parikesit.abimanyu.IT05
        ServerName parikesit.abimanyu.IT05.com
        ServerAlias www.parikesit.abimanyu.IT05.com
        <Directory /var/www/parikesit.abimanyu.IT05/secret>
                Deny From All
        </Directory>
	<Directory /var/www/parikesit.abimanyu.IT05>
        	RewriteEngine On
        	RewriteRule ^public/images/abimanyu\.png$ - [L]
        	RewriteCond %{REQUEST_URI} abimanyu
        	RewriteRule \.(jpg|png)$ /public/images/abimanyu.png [R=301,L]
        </Directory>

        Alias "/js" "/var/www/parikesit.abimanyu.IT05/public/js"
        ErrorDocument 403 /error/403.html
        ErrorDocument 404 /error/404.html
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
<VirtualHost *:14000 *:14400>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/rjp.baratayuda.abimanyu.IT05
        ServerName rjp.baratayuda.abimanyu.IT05.com
        ServerAlias www.rjp.baratayuda.abimanyu.IT05.com
        <Directory /var/www/rjp.baratayuda.abimanyu.IT05>
                AuthType Basic
                AuthName "Restricted Files"
                # (Following line optional)
                AuthBasicProvider file
                AuthUserFile "/etc/apache2/passwd/password"
                Require user Wayang
        </Directory>
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>'

echo "$gg" > /etc/apache2/sites-available/000-default.conf
service apache2 restart
```
