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

Zabbix kurulum ekranı gelmişse zabbix kurulumunu başarıyla tamamlamışız.<figure class="wp-block-image">

<img src="http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/setup1-1024x613.jpg" alt="" class="wp-image-114" srcset="http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/setup1-1024x613.jpg 1024w, http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/setup1-300x180.jpg 300w, http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/setup1-768x460.jpg 768w, http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/setup1.jpg 1098w" sizes="(max-width: 706px) 89vw, (max-width: 767px) 82vw, 740px" /> </figure> 

Kurulum için gelmesi gereken sayfalar aşağıda.<figure class="wp-block-image">

<img src="http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/setup2-1024x634.jpg" alt="" class="wp-image-116" srcset="http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/setup2-1024x634.jpg 1024w, http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/setup2-300x186.jpg 300w, http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/setup2-768x475.jpg 768w, http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/setup2.jpg 1104w" sizes="(max-width: 706px) 89vw, (max-width: 767px) 82vw, 740px" /> </figure> 

Database ismini kurduğumuz ismi yazıp parola tanımlıyoruz. <figure class="wp-block-image">

<img src="http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/setup3-1024x632.jpg" alt="" class="wp-image-117" srcset="http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/setup3-1024x632.jpg 1024w, http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/setup3-300x185.jpg 300w, http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/setup3-768x474.jpg 768w, http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/setup3.jpg 1105w" sizes="(max-width: 706px) 89vw, (max-width: 767px) 82vw, 740px" /> </figure> <figure class="wp-block-image"><img src="http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/setup4.jpg" alt="" class="wp-image-118" srcset="http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/setup4.jpg 673w, http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/setup4-300x185.jpg 300w" sizes="(max-width: 673px) 100vw, 673px" /></figure> <figure class="wp-block-image"><img src="http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/setup5.jpg" alt="" class="wp-image-119" srcset="http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/setup5.jpg 687w, http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/setup5-300x191.jpg 300w" sizes="(max-width: 687px) 100vw, 687px" /></figure> 

Kurulumu tamamladıktan sonra karışımıza login ekranı geliyor. Eğer gelmediyse login ekranına [http://server\_login\_ip/zabbix/index.php](http://34.90.201.174/zabbix/index.php) ile ulaşabilirsiniz. Eğer girdiğiniz kullanıcı adı ve parola ile giriş yapamadıysanız default olarak gelen id ve parolayı deneyebilirsiniz.  
**Username: admin  
Parola : zabbix** <figure class="wp-block-image">

<img src="http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/login.jpg" alt="" class="wp-image-120" srcset="http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/login.jpg 547w, http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/login-272x300.jpg 272w" sizes="(max-width: 547px) 100vw, 547px" /> </figure> <figure class="wp-block-image"><img src="http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/dashbord.jpg" alt="" class="wp-image-121" srcset="http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/dashbord.jpg 742w, http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/dashbord-300x164.jpg 300w" sizes="(max-width: 706px) 89vw, (max-width: 767px) 82vw, 740px" /></figure> 

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

#### Zabbıx&#8217;e Host ekleme ve Monıtörleme

Configuration sekmesinin altındaki host sekmesine giriyoruz ve Create Host butonuna tıklıyoruz<figure class="wp-block-image">

<img src="http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/host1.1-1024x384.jpg" alt="" class="wp-image-122" srcset="http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/host1.1-1024x384.jpg 1024w, http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/host1.1-300x113.jpg 300w, http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/host1.1-768x288.jpg 768w" sizes="(max-width: 706px) 89vw, (max-width: 767px) 82vw, 740px" /> </figure> 

Gelen ekranda monitörleme yaparken cihazı hangi isimle göreceğimizi yazıyoruz. Grup kısmına linux server ekleyebilirsiniz. IP adresi olarakta monitörleme yapmak istediğimiz, zabbix-agent kurulu olan sunucunun ip adresini giriyoruz.<figure class="wp-block-image">

<img src="http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/host2.1-1024x762.jpg" alt="" class="wp-image-123" srcset="http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/host2.1-1024x762.jpg 1024w, http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/host2.1-300x223.jpg 300w, http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/host2.1-768x572.jpg 768w, http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/host2.1.jpg 1253w" sizes="(max-width: 706px) 89vw, (max-width: 767px) 82vw, 740px" /> </figure> 

Ardından Templates eklememiz gerekiyor. Templates&#8217;ler bize Zabbix&#8217;in kendi içerisinde bulunan şablonları sunucumuzu monitörlerken kullanmamız için olanak sağlıyor. İnternetten Custom Template bulup onlarıda ekleyebiliriz. İlk olarak deneme amaçlı OS Linux template ekliyoruz. Daha sonra başka template eklemesi yapabilirsiniz.<figure class="wp-block-image">

<img src="http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/host3.1-1024x417.jpg" alt="" class="wp-image-124" srcset="http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/host3.1-1024x417.jpg 1024w, http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/host3.1-300x122.jpg 300w, http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/host3.1-768x313.jpg 768w, http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/host3.1.jpg 1071w" sizes="(max-width: 706px) 89vw, (max-width: 767px) 82vw, 740px" /> </figure> <figure class="wp-block-image"><img src="http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/host4.1-1024x595.jpg" alt="" class="wp-image-125" srcset="http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/host4.1-1024x595.jpg 1024w, http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/host4.1-300x174.jpg 300w, http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/host4.1-768x446.jpg 768w, http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/host4.1.jpg 1416w" sizes="(max-width: 706px) 89vw, (max-width: 767px) 82vw, 740px" /></figure> <figure class="wp-block-image"><img src="http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/host5.1-1024x493.jpg" alt="" class="wp-image-126" srcset="http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/host5.1-1024x493.jpg 1024w, http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/host5.1-300x144.jpg 300w, http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/host5.1-768x370.jpg 768w, http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/host5.1.jpg 1416w" sizes="(max-width: 706px) 89vw, (max-width: 767px) 82vw, 740px" /></figure> 

Hostumuzu Ekledikten sonra yine hosts sekmesinin altından kontrol edebiliriz. Sağ taraftaki ZBX butonu yeşil yanıyor ise host ekleme işlemini de tamamlamışız demektir. Işığın yanması için biraz beklememiz gerekiyor.  
**Eğer uzun süre beklediniz ve ZBX ışığı yanmadı ise** zabbix-agent kurulumunuzda hata vardır. &#8220;systemctl status zabbix-agent&#8221; agent servisini kontrol edin veya conf dosyalarınızadaki ip adresi ile host oluştururken kullandığınız ip adreslerinin doğruluğunu kontrol edin.  
**Eğer ZBX butonu kırmızı yanıyıor ise** Zabbix ile zabbix-agent bağlantısı var ama conf dosyalarında bir hata olabilir. Faremizi kırmızı butonun üzerine getirdiğimizde ayrıntılı olarak hatayı öğrenebilirsiniz.  
<figure class="wp-block-image">

<img src="http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/host7.1-1024x122.jpg" alt="" class="wp-image-128" srcset="http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/host7.1-1024x122.jpg 1024w, http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/host7.1-300x36.jpg 300w, http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/host7.1-768x92.jpg 768w" sizes="(max-width: 706px) 89vw, (max-width: 767px) 82vw, 740px" /> </figure> 

ZBX butonu yeşil yanıyorsa bağlantı sağlanmış demektir. Şimdi monitörleme zamanı.

Monitoring sekmesinin altından Graphs sekmesine giriyoruz. Monitörlemek istediğimiz sunucu ve hangi grafiği görmek istediğimizi seçiyoruz. Eğer grafik boş ise biraz beklemeniz lazım.<figure class="wp-block-image">

<img src="http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/monitor111-1024x429.jpg" alt="" class="wp-image-130" srcset="http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/monitor111-1024x429.jpg 1024w, http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/monitor111-300x126.jpg 300w, http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/monitor111-768x322.jpg 768w" sizes="(max-width: 706px) 89vw, (max-width: 767px) 82vw, 740px" /> </figure>
