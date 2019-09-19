---
title: Zabbix kurulumu ve Zabbix-agent ile Sunucu Monitoring (Ubuntu 16.04)
date: 2019-07-01T08:13:25+00:00
layout: post
categories:
---

#Zabbix kurulumu ve Zabbix-agent ile Sunucu Monitoring (Ubuntu 16.04)

Bu yazımızda Zabbix kurulumu ve Zabbix-agent ile sunucu dinlemeden bahsedicez.  
Zabbix, ağlar ve uygulamalar için bir kurumsal açık kaynak izleme çözümüdür. Çeşitli ağ hizmetleri, sunucular ve diğer ağ donanımlarını izlemek ve durumunu takip etmek için tasarlanmıştır.  
Zabbix-agent ise dinleme yapacağımız sunucumuza kurmamız gereken yazılım. Zabbix-agent sayesinde bizim izin verdiğimiz sunucu ile bilgilerimizi paylaşıyoruz.

#### Zabbix Kurulumu

İlk olarak update yapalım

<pre class="wp-block-preformatted">sudo apt install update</pre>

Sonra Zabbix ve Zabbixi kullanmamıza yardımcı olacak apache msql ve php paketlerini indiricez.

<pre class="wp-block-preformatted">sudo apt-get install apache2 libapache2-mod-php 
sudo apt-get install mysql-server 
sudo apt-get install php php-mbstring php-gd php-xml php-bcmath php-ldap php-mysql</pre>

Paket kurulumlarını hallettikten sonra php&#8217;nin konfigürasyon dosyasına kendi saat dilimimizi eklememiz gerekecek. 

<pre class="wp-block-preformatted">sudo nano /etc/php/7.0/apache2/php.ini</pre>

/etc/php dizinin altındaki **7.0 benim kurduğum php versiyonudur**. Sürümler arası değişiklik olacağından sürümünüzü kontrol ederek php.ini dosyasına ulaşın. [php] nin altında kalacak şekilde saat dilimini ekliyoruz.

<pre class="wp-block-preformatted">[Date]
; http://php.net/date.timezone 
date.timezone = 'Europe/Istanbul'</pre>

**Ubuntu 16.04 için** zabbix repolarını sistemimize ekliyoruz.

<pre class="wp-block-preformatted">wget http://repo.zabbix.com/zabbix/3.4/ubuntu/pool/main/z/zabbix-release/zabbix-release_3.4-1+xenial_all.deb</pre>

<pre class="wp-block-preformatted">sudo dpkg -i zabbix-release_3.4-1+xenial_all.deb</pre>

Repoları ekledikten sonra sunucumuzu updateleyip zabbixi indirebiliriz.

<pre class="wp-block-preformatted">sudo apt-get update</pre>

<pre class="wp-block-preformatted">sudo apt-get install zabbix-server-mysql zabbix-frontend-php</pre>

Mysql sunucumuza zabbix için database oluşturmamız gerekiyor. Önce mysqle girelim

<pre class="wp-block-preformatted">mysql -u root -p </pre>

Zabbix için database oluşturalım. Databse ismini zabbixdb olarak oluşturdum. Siz istediğiniz ismi verebilirsiniz.

<pre class="wp-block-preformatted">mysql&gt; CREATE DATABASE zabbixdb;</pre>

Oluşturduğumuz zabbixdb database için zabbix kullanıcısını ekliyoruz. ve @localhost diyerek database erişimini sadece localden yapılmasını dışardan erişimlere izin vermiceğimizi belitiyoruz.

<pre class="wp-block-preformatted">mysql&gt;  GRANT ALL on zabbixdb.* to zabbix@localhost IDENTIFIED BY 'password';</pre>

Yaptığımız değişiklikleri mysql sunucusunda aktif hale getirelim ve mysql&#8217;den çıkalım.

<pre class="wp-block-preformatted">mysql&gt; FLUSH PRIVILEGES;
mysql&gt; EXIT;</pre>

Zabbix veri tabanını şemasını yukarıda oluşturduğumuz veri tabanına ekleyelim.

<pre class="wp-block-preformatted">cd /usr/share/doc/zabbix-server-mysql</pre>

Aşağıdaki komutu çalıştırmadan önce eğer oluşturduğunuz database ismi farklı ise zabbixdb yerine kendi database isminizi yazın.

<pre class="wp-block-preformatted">zcat create.sql.gz | mysql -u root -p zabbixdb</pre>

Şimdi oluştuduğumuz zabbix database&#8217;ini Zabbix&#8217;in conf dosyalarında tanıtalım.

<pre class="wp-block-preformatted">sudo nano /etc/zabbix/zabbix_server.conf</pre>

zabbix_server.conf içerisinde değiştirmemiz gereken satırlar :  
  
DBHost=localhost  
DBName=zabbixdb  
DBUser=zabbix  
DBPassword=password

Bu satırların bazıları dosya içerisinde mevcut. Bu yüzden direk yazmak yerine dosya içerisinde bu satırları bulmanız ve onları değiştirmemiz lazım. (Eğer bu bilgilerin bulunduğu satırlar yorum satırı ise başındaki **#** işaretini silmemiz gerekiyor).  
Bu satırlarda değişiklik yapmamız gereken bilgileri mysql içerisinde database oluştururken kullandığımız bilgilerle değiştirmeliyiz.  
Database oluştururken @localhost diyerek database hostumuzu localhost olarak belirtmiştik  
Aynı sekilde database ismimizi CREATE DATABASE satırında oluşturduğumuz database ismini girdik.  
zabbix@localhost kısmında database kullanıcısını tanıttık ve son olarakta database parolamızı tanıtıyoruz.  
Bu bilgileri kendi oluşturduğunuz database&#8217;e göre değiştirip kaydedip çıkıyoruz.

Zabbix, apache konfigürasyonunu kendisi yapıyor. /etc/zabbix/apache2.conf donyasını güncellemesi için apache ve zabbix servislerini yeniliyoruz.

<pre class="wp-block-preformatted">sudo systemctl restart apache2
sudo systemctl restart zabbix</pre>

Artık zabbix için gerekli olan konfigürasyonları tamamladık. Şimdi zabbix çalışıyormu kontrol edelim. Tarayıcımızdan zabbix&#8217;i açıyoruz.

<pre class="wp-block-preformatted">http://zabbix_sunucusunun_ip_adresi/zabbix/
</pre>

Zabbix kurulum ekranı gelmişse zabbix kurulumunu başarıyla tamamlamışız.

[![setup1.jpg](https://i.postimg.cc/g0YrfTKf/setup1.jpg)](https://postimg.cc/w1G6NFZV)

Kurulum için gelmesi gereken sayfalar aşağıda.

[![setup2.jpg](https://i.postimg.cc/XYvYLC7b/setup2.jpg)](https://postimg.cc/2VPY83Zc) 

Database ismini kurduğumuz ismi yazıp parola tanımlıyoruz. 
[![setup3.jpg](https://i.postimg.cc/fTG8jxv3/setup3.jpg)](https://postimg.cc/xcPG9NhY)
[![setup4.jpg](https://i.postimg.cc/3RNjHRB5/setup4.jpg)](https://postimg.cc/WFRqGsCX)
[![setup5.jpg](https://i.postimg.cc/ZKtf68HW/setup5.jpg)](https://postimg.cc/G9KJCsZR)

Kurulumu tamamladıktan sonra karışımıza login ekranı geliyor. Eğer gelmediyse login ekranına [http://server\_login\_ip/zabbix/index.php](http://34.90.201.174/zabbix/index.php) ile ulaşabilirsiniz. Eğer girdiğiniz kullanıcı adı ve parola ile giriş yapamadıysanız default olarak gelen id ve parolayı deneyebilirsiniz.  
**Username: admin  
Parola : zabbix** <figure class="wp-block-image">
[![login.jpg](https://i.postimg.cc/B6f8mnVB/login.jpg)](https://postimg.cc/mcV2DBRP)
[![dashbord.jpg](https://i.postimg.cc/BvTZQcm2/dashbord.jpg)](https://postimg.cc/gw0bgRF0)

Zabbix-agent kurulumundan sonra **nasıl host eklendiğinden** bahsedicem.

#### Zabbix-agent kurulumu

Zabbix-agent, yazının başındada belirttiğim gibi zabbix kurulu olduğu sunucuya verileri yollayacak servis. Monitörleme yapmak istediğimiz makinaya yüklememiz lazım.

Önce update

<pre class="wp-block-preformatted">sudo apt update</pre>

Update bittikten Zabbix kurulumunda kullandığımız gibi bu sunucumuzada zabbix&#8217;in reposunu eklememiz gerekiyor.

<pre class="wp-block-preformatted">wget http://repo.zabbix.com/zabbix/3.4/ubuntu/pool/main/z/zabbix-release/zabbix-release_3.4-1+xenial_all.deb
</pre>

<pre class="wp-block-preformatted">sudo dpkg -i zabbix-release_3.4-1+xenial_all.deb</pre>

Artık Zabbix-agent kurabiliriz.

<pre class="wp-block-preformatted">sudo apt install zabbix-agent</pre>

Agent kurulumu tamamlandıktan sonra agent&#8217;ın hangi sunucuya verileri yollaması gerektiğinin konfigürasyonunu yapıcaz. 

<pre class="wp-block-preformatted">sudo nano /etc/zabbix/zabbix_agentd.conf</pre>

.conf dosyasının içerisinde aşağıdaki gibi server satırını bulup ip adresine Zabbix, kurduğumuz sunucunun ip adresini yazıyoruz.

<pre class="wp-block-preformatted">Server=127.0.0.1</pre>

.conf dosyasındaki değişiklikleri tamamladığımızda aktif hale gelmesi için agent servisini yeniden bağlatıyoruz.

<pre class="wp-block-preformatted">sudo systemctl restart zabbix-agent</pre>

<pre class="wp-block-preformatted">sudo systemctl enable zabbix-agent</pre>

Agent servisini yeniden başlattıktan sonra durumunu kontrol edip hata olup olmadığına bakalım.

<pre class="wp-block-preformatted">systemctl status zabbix-agent</pre>

Eğer hata yoksa agent kurulumunu başarıyla tamamlamışız. Fakat tabiki hata olup olmadığının kesin sunucumuzu monitörlemek için Zabbix&#8217;e eklediğimizde anlayacağız.

#### Zabbıx'e Host ekleme ve Monıtörleme

Configuration sekmesinin altındaki host sekmesine giriyoruz ve Create Host butonuna tıklıyoruz

[![host1-1.jpg](https://i.postimg.cc/nMbwz7RQ/host1-1.jpg)](https://postimg.cc/jnch3Wrs)

Gelen ekranda monitörleme yaparken cihazı hangi isimle göreceğimizi yazıyoruz. Grup kısmına linux server ekleyebilirsiniz. IP adresi olarakta monitörleme yapmak istediğimiz, zabbix-agent kurulu olan sunucunun ip adresini giriyoruz.
[![host2-1.jpg](https://i.postimg.cc/PxD7k2Kk/host2-1.jpg)](https://postimg.cc/WF28GmQ9)

Ardından Templates eklememiz gerekiyor. Templates&#8217;ler bize Zabbix&#8217;in kendi içerisinde bulunan şablonları sunucumuzu monitörlerken kullanmamız için olanak sağlıyor. İnternetten Custom Template bulup onlarıda ekleyebiliriz. İlk olarak deneme amaçlı OS Linux template ekliyoruz. Daha sonra başka template eklemesi yapabilirsiniz.
[![host3-1.jpg](https://i.postimg.cc/xTf6sfrQ/host3-1.jpg)](https://postimg.cc/hhY8vqPZ)
[![host4-1.jpg](https://i.postimg.cc/3JNg4d5k/host4-1.jpg)](https://postimg.cc/ppN93Vdv)
[![host5-1.jpg](https://i.postimg.cc/ydnZWzLh/host5-1.jpg)](https://postimg.cc/FddRnw51)

Hostumuzu Ekledikten sonra yine hosts sekmesinin altından kontrol edebiliriz. Sağ taraftaki ZBX butonu yeşil yanıyor ise host ekleme işlemini de tamamlamışız demektir. Işığın yanması için biraz beklememiz gerekiyor.  
**Eğer uzun süre beklediniz ve ZBX ışığı yanmadı ise** zabbix-agent kurulumunuzda hata vardır. &#8220;systemctl status zabbix-agent&#8221; agent servisini kontrol edin veya conf dosyalarınızadaki ip adresi ile host oluştururken kullandığınız ip adreslerinin doğruluğunu kontrol edin.  
**Eğer ZBX butonu kırmızı yanıyıor ise** Zabbix ile zabbix-agent bağlantısı var ama conf dosyalarında bir hata olabilir. Faremizi kırmızı butonun üzerine getirdiğimizde ayrıntılı olarak hatayı öğrenebilirsiniz.  

[![host7-1.jpg](https://i.postimg.cc/K8n4R2zK/host7-1.jpg)](https://postimg.cc/xX194BLY)

ZBX butonu yeşil yanıyorsa bağlantı sağlanmış demektir. Şimdi monitörleme zamanı.

Monitoring sekmesinin altından Graphs sekmesine giriyoruz. Monitörlemek istediğimiz sunucu ve hangi grafiği görmek istediğimizi seçiyoruz. Eğer grafik boş ise biraz beklemeniz lazım.

[![monitor111.jpg](https://i.postimg.cc/K8Jjn5PF/monitor111.jpg)](https://postimg.cc/75Jq40tQ)
