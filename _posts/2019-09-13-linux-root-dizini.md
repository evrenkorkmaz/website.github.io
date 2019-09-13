---
title: Linux Kök Dizini
date: 2019-07-14T21:04:00+00:00
layout: post
categories:
---
 # Linux Kök Dizini 


### /bin ve /usr/bin 

İşeltim sisteminin çalışması için gerekli programların bulunduğu konum.
Örneğin; 
- Bash programları: bash - sh 
- Dosya düzenleme programları: tar,cat,echo,vim,nano,ls
- Procces programları: kill,grep

### /sbin ve /usr/sbin

Sistem yöneticilerinin (root) kullanabileceği komutların tutulduğu yerdir.
Örneğin;
 - init
 - getty
 - updatedb ...

### /boot

Bilgisayar başlatıldığında ilk olarak yüklenmesi gereken programların bulunduğu yerdir.
Örneğin;
 - Linux çekirdeği (kernel)
 - vm-linuz

### /dev 

Girdi-Çıktı dosyaları ve bilgisayarın desteklediği tüm aygıtların bilgisi bu dizindedir. Linuxtaki aygıtlar ya karakter aygıtı yada blok aygıtlardır.
Örneğin;
 - Blok aygıtlar: Disket,CD-ROM, sabit disk ...
 - Karakter aygıt: fare,klavye, seri port ...

### /etc 

Bu dizinde bir çok programın ve sistemin yapılandırılma dosyaları vardır. 
Örneğin; nginx yapılandırması-konfigürasyonu ile ilgili gerekli dosyalar /etc/nginx içerisinde saklanır.

**/etc altındaki alt dizinler**

 - /etc/skel : Kullanıcı hesabı açtığında kullanıcı dizinine kopyalanan dosyaların bulunduğu konum
 - /etc/fstab : Çeşitli dosya sistemlerinin ve bağlantılı komutların (mount,pointer) ayrıntılı açıklamalarını tutan konum.
 - /etc/inittab : Sistem başlatıldığında yada herhangi bir çalışma anında hangi proseslerin çalışmakta olduğunu gösteren dosya.
 - /etc/rc.d : rc(run command) dosyalarının olduğu bölümdür. rc dosyaları başlangıç betiklerini(script) içerir. Yani programın nasıl başlaması gerektğini kontrol eder. Mesela "vim" başlatıldığında hangi ayarlarla başlaması gerektiğini ".vimrc" dosyasından kontrol eder. rc.0,rc.1...rc.6 olan dosyalar ise bilgisayarın hangi sevitesinde çalışacağına dair bilgi verir.
 - /etc/passwd : Kullanıcılara ait çeşitli bilgilerin bulunduğu ve kullanıcıların tanımlandığı bölümdür.
 - /etc/motd : Kullanıcı sistemi başlattığında ekrana basılması gereken mesajların bulunduğu konum.
 - /etc/profile : Sisteme girildiğinde çalıştırılması istenen dosyaların saklandığı yer.

### /lib 

Programlar tarafından paylaşılan kütüphane dosyalarının (perl,c,python...) ve sistem başlatılırken kullanılan yüklenebilir sürücü modellerinin saklandığı yer.


### /mnt 

USB,CD,disketlerin ön tanımlı olarak bağlandığı (mount edildiği) yerdir. Bazı işletim sistemlerinde media altına da bağlanabilmektedir.

### /var 

Programların değişken sistem dosyalarının tutulduğu konumdur.
Örneğin; Programalrın çalışma sırasında kullandığı uygulamaların anlık durumları(/var/lib), yazıcıya gönderilen sayfalar, sistem hatalarını yada mesajların kayıtlar (/var/log) , o anda çalışan proseslerin bilgilerini tutan dosya (/var/run)

### /tmp 

Geçici dosyaların tutulduğu yerdir. Sistem başlatıldıktan sonra bu işi /var/tmp üstlenir. Sistem çalışırken bu dosya içerisindeki oynalamalar sistemde bazı bozulmalara neden olabilir. Dikkatli olmak lazım.

### /usr 

Bütün komutların,kütüphanelerin, kullanma klavuzlarının ve sistemin normal işleyişi için gerekli olan dosyaların bulunduğu yerdir. Linux sisteminde bir uygulama yüklencekse ön tanımlı olarak buraya eklenir. 

**/usr altındaki alt dizinler**

 - /usr/bin ve /usr/sbin : Nerdeyse bütün komut dosyalarının bulunduğu konum.
 - /usr/local : Local olarak yüklenen dosyaların bulunduğu konum.
 - /usr/src : Linux çekirdeğinin kaynak kodlarının bulunduğu konum.
 
















 















 



















