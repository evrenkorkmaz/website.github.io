---
title: "Google Cloud ile Authoritative-Only BIND DNS Server Kurulumu (Ubuntu 16.04)"
date: 2019-06-27T14:00:58+00:00
layout: post
categories:
---

#Google Cloud ile Authoritative-Only BIND DNS Server Kurulumu (Ubuntu 16.04)

Bu yazımızda Google Cloud üzerinden oluşturduğumuz DNS serverlar ile kendi web sitemizin bu DNS serverlar üzerinden nasıl bağlanılacağından bahsedicez. Bunun için &#8220;**master**&#8221; ve &#8220;**slave**&#8221; olacak 2 adet sunucu kurmamız gerekiyor. 2 adet sunucu kurmamızın sebebi durumun sürekliliğini sağlamak. &#8220;**master**&#8221; sunucumuzdaki zone kayıtları &#8220;**slave**&#8221; sunucumuza aktarılacak. Böylece &#8220;**maste**r&#8221; sunucusunun patlaması durumunda DNS çalışmaya devam edecektir( Evet isterseniz fazladan **&#8220;slave&#8221;** sunucu kurabilirsiniz).

Kuruluma geçmeden önce DNS kurulumunda kullanıcağımız yapıları tanıyalım.

#### _Authoritative DNS name server_ 

Bir zone veya domain için yapılan tüm DNS sorgularından sorumludur.

Yapısı:

  * Primary server (Master Server)
  * Secondary server (Slave Server) 

_Authoritative_ serverların yapısını ve amacını tam olarak anlamak için Non-_Authoritative_ serverlara bir göz atalım.

_**Non-Authoritative DNS**_ **:**  __Authoritative server ile farkı herhangi bir zone veya domain için yetkilendirilmemiştir. Sorgu yapılacağı zaman _Authoritative_ serverlardan bilgileri alır ve TTL(Time-to-Live) süresince cahce&#8217;te tutar. Böylelikle zamandan kazanç sağlamakla birlikte bandwidth trafiği de fazla etkilenmez.

#### DNS

DNS (Domain Name System), internet ortamında alan adı-IP dönüşümlerini gerçekleştiren sistemdir. Bu işlem, hatırlaması zor olan IP adreslerini kullanmak yerine, hatırlaması daha kolay olan host isimlerinin kullanımına izin verir.

İnternet üzerinden bağlanabildiğimiz herşeyi IP ler ile sağlıyoruz. Fakat her giridiğimiz sitenin ipsini ezberlemek tabikide çok zor. Rehberinizdeki tüm numaraları ezberlemek zorunda olduğunuzu düşünün. DNS size bu kolaylığı sağlıyor.

#### Server oluşturma

Ben sunucularımı Google Cloud üzerinden oluşturdum. 

Compute Engine > Sanal Makine Örnekleri > Örnek oluştur adımlarını izleyerek sunucumuzu oluşturuyoruz 

Makine türü olarak küçük 1vCPU, işletim sistemi olarak Ubuntu 16.04 işletim sistemini seçtim. HTTP ve HTTPS trafiğine izin veriyoruz. master ve slave sunucu içinde aynı özelliklerde 2 sunucu oluşturdum. 

#### Server IP Adresleri

Birazyan yapacağım konfigürasyonlar için master, slave ve sitemizin iplerini kullanıcam.

  * master server ip : 34.90.167.146
  * slave server ip : 34.90.140.220
  * Web sitesi ip : 34.90.100.120

#### Master Sunucu konfigürasyonu

İlk olarak makinemiz yeni olduğu için güncelleme yaparak başlayalım.

<pre class="wp-block-preformatted">sudo apt-get update</pre>

Bind için gerekli olan paketleri kuralım.

<pre class="wp-block-preformatted">sudo apt-get install bind9 bind9utils bind9-doc</pre>

Sunucumuzu ipv4’e göre ayarlamamız gerekiyor. /etc/default/bind9 dosyasını konfigüre edelim.

<pre class="wp-block-preformatted">sudo nano /etc/default/bind9</pre>

Dosya içeriği böyle olmalı :

<pre class="wp-block-preformatted"># run resolvconf?
RESOLVCONF=no

# startup options for the server
OPTIONS="-u bind"
</pre>

OPTIONS=&#8221;-u bind&#8221; >> OPTIONS=&#8221;-4 -u bind&#8221; olarak değiştirelim.

<pre class="wp-block-preformatted"># run resolvconf?
RESOLVCONF=no

# startup options for the server
OPTIONS="<strong>-4</strong> -u bind"</pre>

Bind klasöründe &#8220;**named.conf.options**&#8221; dosyasının konfigürasyonu 

<pre class="wp-block-preformatted">sudo nano /etc/bind/named.conf.options</pre>

<pre class="wp-block-preformatted">options {
        directory "/var/cache/bind";
       <strong> recursion no;
        allow-transfer {none;};</strong>
        dnssec-validation auto;
        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};</pre>

recursion no; ve allow-transfer {none;}; satırlarını ekleyip kaydedip çıkalım. 

  * **recursion no;** > Başka Dns sunucuları ile haberleşmeyi engeller.
  * **allow-transfer {none; };** > Dışarıdan gelen zone transferlerini engeller. 
  * Bu satırları ekleyerek sunucumuzun güvenliğini sağlıyoruz. Sadece bizim istediğimiz sunucularla haberleşecek.

**&#8220;na****med.conf.local&#8221;** dosyasının konfigürasyonu

<pre class="wp-block-preformatted">//
// Do any local configuration here
//
zone "evrenkorkmaz.xyz" {
        type master;
        file "/etc/bind/zones/db.evrenkorkmaz.xyz";
        allow-transfer {34.90.140.220;};
};

</pre>

Dosyanın içerisine ilk olarak bunları ekliyelim 

<pre class="wp-block-preformatted">zone "evrenkorkmaz.xyz" {<br />        type master;<br />        file "/etc/bind/zones/db.evrenkorkmaz.xyz";<br />        allow-transfer {34.90.140.220;};<br />};</pre>

  * **zone &#8220;evrenkorkmaz.xyz&#8221; {** // kendi domain adresinizi yazın 
  * **type master;** //master sunucu olduğumuzu belirtiyoruz
  * **file &#8220;/etc/bind/zones/db.evrenkorkmaz.xyz&#8221;;** // Bir sonraki adımda oluşturacağımız zones dosyasında ekliyeceğimiz dosyanın konumunu tanımlıyoruz. İsmini istediğiniz gibi değiştirebilirsiniz ama ne koyduğunuzu unutmayın. 
  * **allow-transfer {34.90.140.220;};** //zone&#8217;ları transfer edeceğimiz slave sunucunun IP adresini yazıyoruz  
    

<pre class="wp-block-preformatted">zone "167.90.34.in-addr.arpa" {
        type master;
        file "/etc/bind/zones/db.34.90.167";
        allow-transfer {34.90.140.220;};
};</pre>

reverse zone için yukarıdaki satırları aynı şekilde named.conf.local dosyasına ekliyoruz.

  * **zone &#8220;167.90.34.in-addr.arpa&#8221; {** // reverse zone kaydı için yazıyoruz. Reverse zone ip adresinden alan adı çözümlemesi yapar. 34.90.167.146 benim master sunucu ip adresim. Bu satır için ip adresini son basamağı olmadan ve tersten yazıyoruz. Son basamak yerine özel bir ada sahip .in-addr.arpa kısmı yazıyoruz. 
  * **type master;** //master sunucu olduğumuzu belirtiyoruz
  * **file &#8220;/etc/bind/zones/db.34.90.167&#8221;;** // Yinde birazdan açacağımız dosyanın konumunu belirtiyoruz.
  * **allow-transfer {34.90.140.220;};** //zone&#8217;ları transfer edeceğimiz slave sunucunun IP adresini yazıyoruz.  
    Bu adımları tamamladıktan dosyamızı kaydedip çıkıyoruz.

# Zone Kayıtları

#### Forward Zone

Forward zone : DNS&#8217;in alan adından ip adresi çözümlemesi yapacağı bölüm.

Şimdi bir üst adımda tanımladığımız zone klasörü ve içindeki dosyaları oluşturucaz.

<pre class="wp-block-preformatted">sudo mkdir /etc/bind/zones</pre>

Bind dizininde zone kayıtları için kullanabileceğimiz bazı örnek dosyalar var. Bunları zone klasorunun altına alalım.

<pre class="wp-block-preformatted">sudo cp /etc/bind/db.local /etc/bind/zones/db.evrenkorkmaz.xyz

sudo cp /etc/bind/db.127 /etc/bind/zones/db.34.90.167</pre>

İlk olarak forward zone kaydı için konfigüre edeceğimiz db.evrenkorkmaz.xyz dosyasıyla başlıyoruz. Dosyanın içini açtığımızda aşağıdaki örnek gibi bir çıktı olması lazım. Eğer farklıysa doğru dosyayı kopyaladığınızdan emin olun yada /etc/bind konumundaki dosyaların içinde bu örneğe benzeyeni kopyalayın.

<pre class="wp-block-preformatted">;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     localhost. root.localhost. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      localhost.
@       IN      A       127.0.0.1
@       IN      AAAA    ::1</pre>

  * **SOA (Start Of Authority)** : Serial, Refresh, Retry, Expire, Negative Cache TTL kayıtlarının tutulduğu yer.
  * **Serial:** Güncellik bilgisi. Serial değeri YYYYAAGGSS (Yıl/ay/gün/sayı) olacak şekilde yazmalıyız. Örnek olarak 28/06/2019 tarihindeki ilk yazdığımız dosya için Serial : 2019062801 olur. Herhangi bir değişiklik yaparsak seriali değiştirmemiz gerekiyor.Bu durumda sondaki sayımızı arttırarak yapıyoruz. Yani eğer değişiklik yaptıysak serialimiz 2019062802 olur. **Her değişiklikten sonra mutlaka arttırmalıyız.**
  * **Refresh:** Zone için ne kadar zamanda bir güncelleme yapacağının bilgisi. Refresh süresini düşük tutarsak interneti sürekli kullanacağı anlamına gelir. Eğer internet hızınıza ve bant genişliğinize güveniyorsanız refresh suresini 20dk-2saat arası yazabilirsiniz. Bunun dışında önerilen refresh süreleri 2-12 saat arasıdır.
  * **Retry:** Zone transferinde bir sorun yaşanırsa yeniden deneme süresi.
  * **Expire:** slave sunucu master sunucusuna bağlanamazsa kayıtların ne kadar saklanacağının bilgisi. Expire süresi ayarlanırken dikkat edilmesi gereken nokta refresh süresinden uzun olması gerekir. Eğer daha kısa olursa herhangi bir bağlantı kesintisinde tekrar bağlanamadan elimizdeki kayıtları kaybetmiş oluruz. Expire süresi ayarlanırken bir diğer dikkat etmemiz gerekecek nokta ise yaşadığımız en uzun bağlantı kesilme süresinin üstünde tutmaktır.
  * **Negative Cache TTL**: Olumsuz bir durum yaşandığında (bağlantı kesilmesi gibi) elindeki datayı ne kadar süre tutacağının (ttl) bilgisi. Önerilen TTL süresi 1-5 gün arası.  
    [kaynak: https://www.ietf.org/rfc/rfc1912.txt](https://www.ietf.org/rfc/rfc1912.txt) (2.2 SOA records).

**DNS kayıt türleri :**

  * **PTR(Pointer Record) :** İp adreslerini domainlere eşleşmesini sağlayan kayıttır.
  * **A (Address) :** İsimleri IP adreslerine eşleyen kayıttır.
  * **AAA (Address ) :** A kaydı ile aynı işi yapar fakat IPV6 için tasarlanmıştır.
  * **CNAME** : Bir host için diğer ad oluşturmak için kullanılır. Aynı domaini işaret eden birden fazla hostname(www, ftp,mail) olması durumunda kullanılır.
  * **NS(Name Server)** : Herhangi bir domain name için geçerli sunucuyu belirten kayıttır.

<pre class="wp-block-preformatted">; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     ns1.evrenkorkmaz.xyz. root.evrenkorkmaz.xyz. (
                     2019062801         ; Serial
                          28800         ; Refresh
                           7200         ; Retry
                        2419200         ; Expire
                            600 )       ; Negative Cache TTL
;
<code>; isim sunucuları</code>
evrenkorkmaz.xyz.    IN      NS      ns1.evrenkorkmaz.xyz. <code>;</code>master isim sunucumu ns1.evrenkorkmaz.xyz olarak tanıttım
evrenkorkmaz.xyz.    IN      NS      ns2.evrenkorkmaz.xyz.  <code>;</code>slave isim sunucumu ns2.evrenkorkmaz.xyz olarak tanıttım
<code>; </code>A kaydı için isim sunucuları 
ns1     IN      A       34.90.167.146 <code>;</code> master sunucu ip adresi
ns2     IN      A       34.90.140.220 <code>;</code> slave sunucu ip adresi
<code>; </code>Diğer A kayıtları
@       IN      A       34.90.100.120 <code>;</code> web sunucu ip adresi
www     IN      A       34.90.100.120 <code>;</code> web sunucu ip adresi</pre>

Sizde bu dosyayı kendi domain ve sunucu adreslerinize göre düzenleyip kaydedin.

#### Reverse Zone

**Reverse zone :** DNS&#8217;in ip adresinden alan adı çözümlemesi yapacağı bölüm.

<pre class="wp-block-preformatted">sudo nano  /etc/bind/zones/db.34.90.167</pre>

Dosyamızın içeriği böyle olmalı : 

<pre class="wp-block-preformatted">;
; BIND reverse data file for local loopback interface
;
$TTL    604800
@       IN      SOA     localhost. root.localhost. (
                              1         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      localhost.
1.0.0   IN      PTR     localhost.</pre>

<pre class="wp-block-preformatted">; BIND reverse data file for empty rfc1918 zone
$TTL    86400
@       IN      SOA     evrenkorkmaz.xyz. root.evrenkorkmaz.xyz. (
                           2019062801   ; Serial
                                28800   ; Refresh
                                 7200   ; Retry
                              2419200   ; Expire
                                  600 ) ; Negative Cache TTL
;
        IN      NS      ns1.evrenkorkmaz.xyz.
        IN      NS      ns2.evrenkorkmaz.xyz.
146     IN      PTR     ns1.evrenkorkmaz.xyz. ; master sunucunun ip adresinin sonu
220     IN      PTR     ns2.evrenkorkmaz.xyz. ; slave sunucunun ip adresinin sonu
120     IN      PTR     www.evrenkorkmaz.xyz. ; web sunucunun ip adresinin sonu</pre>

Sizde bu dosyayı kendi domain ve sunucu adreslerinize göre düzenleyip kaydedin. db.evrenkorkmaz.xyz dosyanını düzenlerken SOA değerlerinin standartlarını yazmıştım. Ordan bakarak sizde kendi SOA kayıtlarınızı düzenleyebilirsiniz.  
PTR kayıtlarının başında sunucuların ip adreslerinin **son basamakları** bulunuyor.

#### Zone Dosyalarının Kontrolü

Zone dosyalarının konfigürasyonları tamamlandıktan sonra kontrol etmemizde fayda var. Bu kontroller için **named-checkconf** ve **named-checkzone** komutlarını kullanıcaz. 

  * **named-checkconf :** Üzerinde değişiklik yaptığımız named.conf.local ve named.conf.options dosyalarında hata olup olmağının kotrolü için.
  * **named-checkzone** : Forward ve reverse zone için oluşturduğumuz dosyaların konrolü için.

<pre class="wp-block-preformatted">sudo named-checkconf</pre>

Herhangi bir şey dönmemişse sıkıntı yok. Devam edebiliriz 

<pre class="wp-block-preformatted">sudo named-checkzone evrenkorkmaz.xyz /etc/bind/zones/db.evrenkorkmaz.xyz</pre>

Sorun yoksa aşağıdaki gibi bir çıktı almanız lazım. 

<pre class="wp-block-preformatted">zone evrenkorkmaz.xyz/IN: loaded serial 6
OK</pre>

<pre class="wp-block-preformatted">sudo named-checkzone 167.90.34.in-addr.arpa /etc/bind/zones/db.34.90.167</pre>

Zone dosyalarınında revers zone kayıtları için olusturuğumuz dosyada yazdığımız gibi master sunucunun ip adresini son hanesi olmadan tersten yazarak son basamağa .in-addr.arpa yazıyoruz 

<pre class="wp-block-preformatted">zone 167.90.34.in-addr.arpa/IN: loaded serial 6
OK</pre>

Çıktımız yine böyle ise sıkıntı yok demektir.  
Buraya kadar hata almadıysanız Bind servisini tekrar başlatarak devam edeibliriz.

<pre class="wp-block-preformatted">sudo service bind9 restart</pre>

Artık son olarak sorun olup olmadığını anlamak için logları izleyebiliriz.

<pre class="wp-block-preformatted">sudo tail -f /var/log/syslog
systemctl status bind9</pre>

Bu komutların çıktılarında bir hata gözükmüyorsa master sunucumuzun konfigürasyonunu başarıyla tamamlamış oluyoruz.

#### Slave Konfigürasyonu

Slave konfigürasyonu master sunucu kadar karışık ve uzun değil. named.conf.local ve named.conf.options dosyalarını düzenleyip servisleri tekrar başlatarak tamamlıcaz.

Fakat makinemiz yeni olduğu için master sunucuda yaptığımız gibi update yaparak başlıyoruz

<pre class="wp-block-preformatted">sudo apt install update</pre>

Bind için gerekli olan paketleri kuralım.

<pre class="wp-block-preformatted">sudo apt-get install bind9 bind9utils bind9-doc</pre>

Sunucumuzu ipv4’e göre ayarlamamız gerekiyor. /etc/default/bind9 dosyasını konfigüre edelim.

<pre class="wp-block-preformatted">sudo nano /etc/default/bind9</pre>

Options satırına yeniden **&#8220;-4&#8221;** ekleyelim

<pre class="wp-block-preformatted"># run resolvconf?
RESOLVCONF=no

# startup options for the server
OPTIONS="<strong>-4</strong> -u bind"</pre>

named.conf.options dosyasına gidiyoruz.

<pre class="wp-block-preformatted">sudo vim /etc/bind/named.conf.options</pre>

master sunucumuzda eklediğimiz satırları ekliyoruz.  
Neden eklediğimizi master sunucumuzu konfigüre ederken anlatmıştık.

<pre class="wp-block-preformatted">options {
        directory "/var/cache/bind";
       <strong> recursion no;
        allow-transfer {none;};</strong>
        dnssec-validation auto;
        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};</pre>

Kaydedip çıkıyoruz. Sırada named.conf.local dosyamız var

<pre class="wp-block-preformatted">sudo vim /etc/bind/named.conf.local</pre>

Dosya içeriğini aşağıdaki gibi düzenliyoruz.

<pre class="wp-block-preformatted">zone "evrenkorkmaz.xyz" {
        type slave;
        file "db.evrenkorkmaz.xyz";
        masters {34.90.167.146;};
};

zone "167.90.34.in-addr.arpa" {
        type slave;
        file "db.34.90.167";
        masters {34.90.167.146;};
};</pre>

Master sunucumuzdan farklı olarak type slave olarak tanımlıyoruz.  
file konumunu değil sadece ismini veriyoruz.  
Allow-transfer yerine master sununun ip adresini tanımlıyoruz.  
Kaydedip çıkıyoruz.

#### Dosyalarının Kontrolü

Master sunucudaki gibi zone dosyalarını oluşturmadığımız için named-checkconf kontrolü ve sistemi yeniden başlatmak yeterli.

<pre class="wp-block-preformatted">sudo named-checkconf
sudo service bind9 restart</pre>

Bu adımları da tamamladıktan sonra hata olup olmadığını anlamak için loglara bakalım.

<pre class="wp-block-preformatted">sudo tail -f /var/log/syslog
sudo service bind9 status</pre>

#### Godaddy Üzerinden Name Server Değiştirme

Ben domainimi ve DNS servisini Godaddy üzerinden aldım. Fakat sizde birazdan uygulayacagım gibi kendi hosting firmanızın sitesi üzerinden isim sunucularınızı tanımlayabilirsiniz. 

Kendi hesabınıza giriş yaptıktan sonra Ürünlerim/DNS/Gelişmiş seçeneklerin altından Ana bilgisayar adları/Ekle diyerek az önce oluşturduğumuz isim sunucularımızı ekliyoruz. İsim sunucularımızı eklerden ns1 ve n2 olarak eklemek yeterli.

Ekledikten sonra yeniden Ürünlerin/DNS/Ad sunucuları/Değiştir/Özel diyerek kendi ad sunucularımızı ekleyebilliriz.

#### Son Kontrol

Son olarak DNS sunucuların kontrolü için **dig** komutu ile kontrol ediyoruz

<pre class="wp-block-preformatted">dig evrenkorkmaz.xyz @localhost</pre>

Answer kısmında kendi web sunucumuzun ip adresini alıyorsak işlemlerimizi başarıyla tamamlamış oluyoruz.

<pre class="wp-block-preformatted">; &lt;&lt;&gt;&gt; DiG 9.10.3-P4-Ubuntu &lt;&lt;&gt;&gt; evrenkorkmaz.xyz @localhost
 ;; global options: +cmd
 ;; Got answer:
 ;; -&gt;&gt;HEADER&lt;&lt;- opcode: QUERY, status: NOERROR, id: 64477
 ;; flags: qr aa rd; QUERY: 1, ANSWER: 1, AUTHORITY: 2, ADDITIONAL: 3
 ;; WARNING: recursion requested but not available
 ;; OPT PSEUDOSECTION:
 ; EDNS: version: 0, flags:; udp: 4096
 ;; QUESTION SECTION:
 ;evrenkorkmaz.xyz.              IN      A
 <strong>;; ANSWER SECTION:
 evrenkorkmaz.xyz.       604800  IN      A       34.90.100.120</strong>
 ;; AUTHORITY SECTION:
 evrenkorkmaz.xyz.       604800  IN      NS      ns1.evrenkorkmaz.xyz.
 evrenkorkmaz.xyz.       604800  IN      NS      ns2.evrenkorkmaz.xyz.
 ;; ADDITIONAL SECTION:
 ns1.evrenkorkmaz.xyz.   604800  IN      A       34.90.167.146
 ns2.evrenkorkmaz.xyz.   604800  IN      A       34.90.140.220
 ;; Query time: 1 msec
 ;; SERVER: 127.0.0.1#53(127.0.0.1)
 ;; WHEN: Fri Jun 28 10:26:35 UTC 2019
 ;; MSG SIZE  rcvd: 129</pre>

Ayrıca [intodns.com](https://intodns.com/) adresinden de kontrol sağlayabilriz.

**Hata Durumları**

Hata almanız durumunda bunları kontrol edebilirniz:

  * İlk olarak 10050 Portunun açık olduğunu kontrol edin.  
    &#8220;**netstat -nltp**&#8221; komutu ile kontrol edebilirsiniz.  
    10050 portu LISTEN durumunda ise sıkıntı yoktur. 
  * 10050 portunun tcp ve udp içinde açık olduğundan emin olun  
    Normal şartlarda yollanılan verinin doğruluğu ve bütünlüğü için tcp kullanılır fakat dns sunucularında hız sağlamak ve sunucuyu yormamak için yollanılan isteklerin cevaplarını udp protokolüyle yollar. Bu yüzden 10050 udp portunu kontrol edin.  
    &#8220;**ufw allow 10050** **udp**&#8221; ile ekleyebilirsiniz.
  * Sunucunuzu herhangi bir platformdan sanal olarak açtıysanınz (Google cloud &#8211; AWS &#8211; Digital ocean vs.) açtığınız site üzerinden sunucuların portlarına ekstradan izin vermeniz gerekiyor olabilir.
  * Zone kayıtları için oluşturduğumuz dosyaların doğruluklarını kontrol edin. Herhangi bir yerinde eksik veya fazla karakter olabilir. Domain adreslerinin sonundaki . (nokta) lar eksik olabilir. İp adreslerini karıştırmış olabilirsiniz. 
  * Zone dosyalarında eğer değişiklik yaptıysanız **Seriali değiştirmeyi unutmuş olabilirsiniz.** Yükseltip tekrar deneyin.
  * Syslog dosyasındaki çıktıları okuyun. Bir hata varsa mutlaka oraya düşer.
