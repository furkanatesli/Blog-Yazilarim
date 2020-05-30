---
Başlık: DNS Server Kurulumu
Paylaşım Tarihi: 13.05.2016
Etiketler: bind9, configure dns server, dns server
---

## DNS Server Kurulumu

Unix Tabanlı Sunucu İşletim Sistemleri üzerinde DNS Sunucu Servisinin çalıştırılması.

İşletim sistemi olarak Linux dağıtımlarından Ubuntu’yu ve DNS Sunucu Servisi olarak da BIND9 paketini seçtik. Ubuntuyu kurduktan sonra DNS Sunucu Servisinin kurulumu ve çalıştırılması için aşağıdaki adımları izliyoruz. Kullanıcının yönetici haklarını açmamız için “sudo su” komutunu yazıyoruz ve belirlediğimiz yönetici parolasını giriyoruz. Problem yaşamamamız ve sistem paketlerini güncellemek için “apt-get update” komutunu yazıyoruz. Güncelleme bittikten sonra BIND9 paketimizi “apt-get install bind9” komutuyla sistemimize kuruyoruz. Ardından kendi DNS Sunucularımızda bulunmayan adresleri sorgulamak için sorguları göndereceğimiz DNS Sunucularını belirliyoruz. Uç birime “gedit /etc/bind/named.conf.options ” komutunu giriyoruz ve dosyaya
````
options {
      directory "/var/cache/bind";
      forwarders {
      8.8.8.8;
      8.8.4.4;
      };

      dnssec-validation auto;
      auth-nxdomain no;    # conform to RFC1035
      listen-on-v6 { any; };
};
````
yazıyoruz ve kaydediyoruz. Biz iletici olarak Google DNS Sunucularını yazdık. Bind9 servisinde yaptığımız değişikliklerin çalışıp çalışmadığını kontrol etmek için servisimizi “service bind9 restart” komutuyla yeniden başlatıyoruz. İsim çözücülerimizin ayarlarını yapmak için “gedit /etc/resolv.conf” komutunu giriyoruz dosyamızda isim sunucusu olarak yerel barındırıcımızın İp’sini(127.0.0.1) giriyoruz. Sunucumuzun çalışıp çalışmadığını kontrol etmek için uç birime “dig www.furkanatesli.com” yazıyoruz.

````
root@furkan:/home/furkan# dig www.furkanatesli.com

...
;; Query time: 3094 msec
;; SERVER: 127.0.0.1#53(127.0.0.1)
...
````
Yaptığımız incelemede www.furkanatesli.com alan adını sunucumuzda ilk kez arattığımız için sorgu zamanı 3094 msec olarak döndü. Aynı sorguyu tekrar yapalım.

````
root@furkan:/home/furkan# dig www.furkanatesli.com

...
;; Query time: 2 msec
;; SERVER: 127.0.0.1#53(127.0.0.1)
...
````

Aynı sorguyu tekrar yaptığımızda alan adı önbelleğimize işlendiği için 2 msec olarak döndü ve bu da sunucumuzun doğru bir şekilde çalıştığını gösterir.
Senaryomuzu:
Alan adımız:gopmyo.com
Web sunucu ip:192.168.6.10
Sunucu barındırıcı adı:ns.gopmyo.com
Sunucu ip:192.168.6.5 olacak şekilde kuruyoruz.
İlk olarak düz sorgularımız için zone bilgi dosyamızı “gedit /etc/bind/db.gopmyo.com” olarak oluşturuyoruz. Dosyamızın içine

````
; BIND data file for local loopback interface
;
$TTL    604800
@ IN SOA ns.gopmyo.com. root.ns.gopmyo.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      ns.gopmyo.com.
@       IN      A       192.168.6.5
@       IN      AAAA    ::1
ns      IN      A       192.168.6.5
www     IN  A     192.168.6.10
````
yazıyoruz.
Ters sorgularımız için ise zone bilgi dosyamızı “gedit /etc/bind/db.192” olarak oluşturuyoruz. Dosyamızın içine

````
; BIND reverse data file for local loopback interface
;
$TTL    604800
@ IN SOA ns.gopmyo.com. root.ns.gopmyo.com. (
                              1         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@      IN      NS      ns.
5      IN      PTR     ns.gopmyo.com.
10     IN      PTR     www.gopmyo.com.
````
yazıyoruz. Zone bilgilerini girdikten sonra zone dosyamızın “gedit /etc/bind/named.conf.local” içine

````
// Düz zone
zone "gopmyo.com" {
        type master;
        file "/etc/bind/db.gopmyo.com";
};
// Ters zone
zone "6.168.192.in-addr.arpa" {
        type master;
        file "/etc/bind/db.192";
};
````
bilgilerini yazıyoruz. Son olarak bind9 servisimizi yeniden başlatıyoruz ve sorgularımızı yapıyoruz.
Düz sorgu:

````
root@furkan:/home/furkan# nslookup www.gopmyo.com
Server:           127.0.0.1
Address:    127.0.0.1#53

Name: www.gopmyo.com
Address: 192.168.6.10
````

Ters sorgu:
````
root@furkan:/home/furkan# nslookup 192.168.6.10
Server:           127.0.0.1
Address:    127.0.0.1#53

10.6.168.192.in-addr.arpa    name = www.gopmyo.com.
````










