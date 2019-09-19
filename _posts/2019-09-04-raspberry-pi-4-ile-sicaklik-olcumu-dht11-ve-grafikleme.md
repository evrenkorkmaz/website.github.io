---
title: Raspberry pi 4 ile Sıcaklık Ölçümü (DHT11) ve Grafikleme
date: 2019-09-04T10:46:57+00:00
layout: post
categories:
---


# Raspberry pi 4 ile Sıcaklık Ölçümü (DHT11) ve Grafikleme

**Proje:**

Bu projemizde raspberry pi4&#8217;te DHT11 sıcaklık sensörü ile okuduğumuz dataları, google cloudda açtığımız sunucudaki influxdb container&#8217;a yollayıp bu datalarla yine aynı sunucumuzdaki grafana container&#8217;ı ile grafikleme yapıcaz.

[![raspisema-768x1024.jpg](https://i.postimg.cc/28PDJkv1/raspisema-768x1024.jpg)](https://postimg.cc/ZCF2vhwm)


**Projede kullanılacak araçlar:**

  * **Raspberry Pi** : öğrenci, amatör ve hobicilerin kullanımına sunulan kredi kartı büyüklüğünde, tek bir board&#8217;dan oluşan mini bilgisayardır.
  * **DHT11 Isı ve Nem Sensör Kartı** : üzerinde DHT11 sensörü bulunan, bağlantıları çekilip breadboard veya farklı kullanımlar için kolaylaştıtılmış hale sokulmuş modüldür. DHT11 sıcaklık ve nem algılayıcı kalibre edilmiş dijital sinyal çıkışı veren gelişmiş bir algılayıcı birimidir.
  * **Docker** : &#8220;Konteynerleştirme&#8221; olarak da bilinen işletim sistemi seviyesinde sanallaştırma sağlayan bir bilgisayar programıdır. Docker, &#8220;konteyner&#8221; adı verilen yazılım paketlerini çalıştırmak için kullanılmaktadır.
  * **InfluxDB :** InfluxData tarafından geliştirilen açık kaynaklı bir zaman serisi veritabanıdır. Go&#8217;da yazılmıştır ve işlem izleme, uygulama ölçümleri, Nesnelerin İnterneti sensör verileri ve gerçek zamanlı analitik gibi alanlarda hızlı, yüksek kullanılabilirlikli depolama ve zaman serisi verilerinin alınması için optimize edilmiştir.
  * **Grafana** : Web uygulaması olarak çalışan, açık kaynak kodlu, genel amaçlı pano ve grafik programıdır. Grafit, InfluxDB veya opentsdb&#8217;yi yardımcı olarak destekler.

**Raspberry pi 4 ve DHT11**

<ul class="wp-block-gallery columns-1 is-cropped">
  <li class="blocks-gallery-item">
   [![raspi-768x432.jpg](https://i.postimg.cc/NMrVRTJb/raspi-768x432.jpg)](https://postimg.cc/ygsvHJzS)
  </li>
</ul>

Bu projemizde raspberry üzerinde takılı grove pi shield kullanıyoruz. Grove pi shieldı kullanmak için github üzerinden [grove pi reposunu](https://github.com/DexterInd/GrovePi) indirerek kullanabiliriz. Repodaki read.me kısmındada olduğu gibi kolay kurulum için aşağıdaki komutları çalıştırarak grove pi shieldı kullanmaya başlayabiliriz.

<pre class="wp-block-preformatted">curl -kL dexterindustries.com/update_grovepi | bash
cd Firmware bash 
firmware_update.sh
reboot</pre>

Kuurulum işleminden sonra Grove Pi üzerine bağladığımız DHT11 in kontrolü için aşağıdaki komutu çalıştırıyoruz.

<pre class="wp-block-preformatted">sudo i2cdetect -y 1</pre>

<pre class="wp-block-preformatted"><code> 0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f</code>
 00:      -- 04 -- -- -- -- -- -- -- -- -- -- -- 
 10: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
 20: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
 30: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
 40: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
 50: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
 60: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
 70: -- -- -- -- -- -- -- --  </pre>

DHT11 Sensörü Grove Pi üzerinde D4 portuna bağlı. &#8220;sudo i2cdetect -y 1&#8221; komutunu çalıştırdığımızda sensörü görüp görmedğiniği kontrol edebiliriz. Eğer komutun çıktısında bütün satırlarda &#8220;&#8211;&#8221; var ise sensörü görmüyo demek.Bu durumda Grove Pi reposunun kurulum kısmında hata olabilir.

Sensörün kontrolünü sağladıktan sonra python ile sensördeki dataları okumaya çalışcaz. Bir dosya oluşturup bu dosyanın içerisine giriyoruz. Yazacağımız python kodunda önce değerleri okuyup bu değerleri data.txt içerisine atıcaz. data.txt&#8217;deki datalarıda sunucumuzda oluştruduğumuz influxdb database&#8217;e göndericez.

<pre class="wp-block-preformatted">nano dht.py</pre>

<pre class="wp-block-preformatted">import grovepi #grove pi kütüphanesini ekliyoruz
dht_sensor_port=4 
#dht11 sensörümüzün takılı olduğu port. Bizimki D4 poruna takılı
dht_sensor_type=0
#Grove pi kütüphanesi içerisindeki dht fonksiyonlarını kullanıcaz. Bu fonksiyon içerisinde type değişkenini tanımlamamız lazım. 
#Type değişkeni için değerler
# 0 - DHT11
# 1 - DHT22
data=open('data.txt','w') #data.txt içerisine yazmak için dosyayı açıyoruz.
[ temp,hum ] = grovepi.dht(dht_sensor_port,dht_sensor_type)
#grovepi kütüphanesindeki dht fonksiyonunu kullanarak sensörün takılı olduğu portu ve type değişkenini tanımlıyoruz.
# Tanımladığımız parametrelerle gelen dataları temp ve hum değişkenlerinin içerisine atıyoruz.
print ('Temp: '+ str(temp) + '*C' + '\tHumidity:' + ' %'+ str(hum)) #aldığımız dataları görmek için ekrana bastırıyoruz.
data.write('temperature ' 'temp='+ str(temp)) # Aldığımız dataları data.txt içerisine yazıyoruz.
data.write('\n')
data.write('temperature ' + 'hum='+ str(hum))</pre>

Kodu kaydedip çıktıktan sonra içerisine yazması için data.txt dosyasını oluşturalım, kodumuzu çalıştıralım ve data.txt dosyasını komtrol edelim. 

<pre class="wp-block-preformatted">touch data.txt
python3 dht.py
cat data.txt</pre>

<pre class="wp-block-preformatted">temperature temp=23.0
temperature  hum=21.0</pre>

data.txt içerisi yukarıdaki gibi gözükmesi lazım. İleriki adımlarda bu dataları &#8220;influxdb&#8221; içerisine ekleyeceğimiz zaman bu formatta yollamamız lazım. Buraya kadar sorunsuz geldiyseniz şimdilik raspberry pi ile işimiz bitti.

**Docker ile Influxdb ve Grafana**

Projemizin devamında giriş kısmındada belittiğim gibi google cloud üzerinden sunucu oluşturup Docker yardımıyla Influxdb ve grafana kurup devam edicez. İşlemlerimin devamında Google cloud üzeründe Ubuntu 18.04 g1-small CPU&#8217;lu bir sunucu açıp devam ediyorum. 

İlk olarak sunucumuzu update edip Docker kurulumu yapıyoruz.

<pre class="wp-block-preformatted">sudo apt-get update
sudo apt-get install docker.io
sudo systemctl start docker
sudo docker --version</pre>

Yukarudaki komutları çalıştırdıktan sonra **docker --version** ile kontrol ediyoruz. Eğer versiyon bilgisi dönüyorsa sıkıntı yok demektir. Docker kurulumundan sonra ilk olarak Docker ile Influxdb imajını indirip konteyneri run&#8217;lıyoruz. 

Komut içerisindeki &#8220;**-d**&#8221; parametresi arkaplanda çalışmasını sağlıyor.  
**&#8220;-p&#8221;** parametresi port atamaları için. Influxdb&#8217;nin default olarak 8083 portunda çalışsan bir web arayüzü ve 8086 portunda Inluxdb API bulunuyor. Bu portları vererek çalıştırmamız gerekiyor.

<pre class="wp-block-preformatted">sudo docker pull influxdb
sudo docker run -d -p 8083:8083 -p 8086:8086  influxdb</pre>

Komutun işlemleri bittikten sonra &#8220;**sudo docker ps** &#8221; komutu ile konteynerin çalışıp çalışmadığını kontrol edebilirsiniz. 

<pre class="wp-block-preformatted">CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                                            NAMES
246cd4aed60a        influxdb            "/entrypoint.sh infl…"   3 seconds ago       Up 2 seconds        0.0.0.0:8083->8083/tcp, 0.0.0.0:8086->8086/tcp   kind_galois</pre>

Konteynerimizin arka planda çalıştığını gördük. Şimdi düzgün çalışıp çalışmadığını kontrol edelim. Influxdb kurulu olan sunucumuzun 8083 portuna tarayıcıdan bağlanıyoruz.

<pre class="wp-block-preformatted">http://influxdb.sunucu.ip.adresi:8083</pre>

<ul class="wp-block-gallery columns-1 is-cropped">
  <li class="blocks-gallery-item">
   [![influxx-768x342.png](https://i.postimg.cc/hv4pGdd2/influxx-768x342.png)](https://postimg.cc/Q9Pp4VS7)e>
  </li>
</ul>

Bağlantıyı sağladığımızda influxdb&#8217;nin web arayüzü yukarıdaki gibi gelmesi lazım. Web arayüzünden database işlemlerini kolayca yapabiliriz. Öncelikle bir database oluşturalım. Query: bölümünün içerisine &#8220;**CREATE DATABASE** db-name&#8221; şeklinde database oluşturabiliriz. Benim oluşturacağım database ismi &#8220;temp&#8221; olacak

<pre class="wp-block-preformatted">Query: CREATE DATABASE "temp"</pre>

Database oluşturduktan sonra bu database üzerinden sorgu yapacağımızın için sağ üstteki &#8220;Database:&#8221; kısmından eklediğimiz database&#8217;i seçelim. Şimdilik Influxdb ile işimiz bitti. Şimdi Raspberry e geri dönüp yazdığımız python kodundaki aldığımız dataları oluşturduğumuz database&#8217;e yollayalım. Influxdb&#8217;ye yollayacağımız dataların formatı ve nasıl yollayacağımız ile ilgili döküman mevcut. [influxdb-dataformat](https://docs.influxdata.com/influxdb/v1.7/write_protocols/line_protocol_tutorial/) . Syntax bölümüne bakalım. 

<pre class="wp-block-preformatted"><code>weather,location=us-midwest temperature=82 1465839830100400200 </code>
<code>|         --------------------     --------------       |  </code>
<code>|                  |                  |                 |  </code>
<code>|                  |                  |                 |  </code>
<code>+----------+----+--------+------+---------+----+---------+ </code>
<code>|measurement    |,tag_set|      |field_set|    |timestamp| </code>
<code>+----------+----+--------+------+---------+----+---------+ </code>
</pre>

İlk gireceğimiz değer **measurement key value**. Vigül ile ayıracağımız kısımlar **tag key value**. İlk boşluktan sonraki değer tablomuza eklememiz gereken integer değerler yani **field key values**. Son kısımada timestamp ekleyebiliriz. Bizim data.txt dosyamızda **measurement key value**, bir sonraki adımda ekleyeceğimiz lokasyon bilgisi yani **tag key value** ve son olarakta sensörden okuduğumuz sıcaklık ve nem bilgisini yollayacaz. Timestap&#8217;e ihtiyacımız yok çünkü dataları tek tek yollayacağımız için influxdb üzerinde dataların geldiği zaman bilgisini eklediğinden eklemeye gerek duymuyoruz. Raspberry içerisindeki data.txt&#8217;ye yeniden bakalım. 

<pre class="wp-block-preformatted">temperature temp=23.0
temperature hum=21.0</pre>

Şimdi bu dataları ilk olarak elle yollayıp test edelim. Yukarıdada belirttiğim data format linkinde &#8220;curl&#8221; ile Influxdb api&#8217;ya dataları yollayabiliriz. Bunun için gerekli komut: 

<pre class="wp-block-preformatted">curl -i -XPOST "http://influx.ip.addres:8086/write?db=temp" --data-binary @data.txt</pre>

İnfluxdb&#8217;nin web sayfası 8083 portunda. API ise 8086 portunda. Bu yüzden influxdb kurulu olan makinanın ip adresi:8086&#8217;yı ekleylip yaynına &#8220;/write?db=temp&#8221; ile hangi database&#8217;e ekleyeceğimizin bilgisini ekliyoruz. Komutun devamında &#8211;data-binary kısmında data.txt dosyamızı gösteriyoruz. @data.txt yerine elle yollamak için **&#8211;data-binary &#8220;yollamak istediğimiz data&#8221;** şeklindede komutu çalıştırabiliriz. Yukarıdaki komutu çalıştırıp Influxdb&#8217;yi kontrol edelim. Komutu çalıştırınca raspberry&#8217;te aşağıdaki gibi onay cıktısı gelmesi lazım.

<pre class="wp-block-preformatted">HTTP/1.1 204 No Content
 Content-Type: application/json
 Request-Id: bbfe0ce8-ceee-11e9-8007-0242ac110002
 X-Influxdb-Build: OSS
 X-Influxdb-Version: 1.7.8
 X-Request-Id: bbfe0ce8-ceee-11e9-8007-0242ac110002
 Date: Wed, 04 Sep 2019 08:33:56 GMT</pre>

Bu işlemden sonra Influxdb web sayfasında geçip sağ yukarıdan oluşturduğumuz &#8220;temp&#8221; database&#8217;sini seçip Query kısmından aşağıdaki sorguyu yapıyoruz. Bu sorguda bu sağ üstte seçtiğimiz temp databaseindeki temperature keyine sahip satırları listeliyor.

<pre class="wp-block-preformatted">SELECT * FROM temperature</pre>

<ul class="wp-block-gallery columns-1 is-cropped">
  <li class="blocks-gallery-item">
  
  [![influx-sorgu.png](https://i.postimg.cc/nzXqSGqb/influx-sorgu.png)](https://postimg.cc/cgNKrYDX)
  </li>
</ul>

Yukarıdaki gibi dataların gelip gelmediğini kontrol ettikten sonra bunu otomatik olarak sürekli yollamasını sağlayacak basit bir script yazalım. 

<pre class="wp-block-preformatted">nano temp.sh</pre>

<pre class="wp-block-preformatted">!/bin/bash
 while true; do
     python3 temp.py
     curl -i -XPOST "http://influxdb.ip.addres:8086/write?db=db-name" --data-binary @data.txt
     sleep 5s
 done</pre>

temp.sh içerisini yukarıdaki gibi düzenleyip çalıştırdığımızda 5 saniyede bir temp.py çalıştırıp okuduğu dataları database&#8217;e yollayacak.

<pre class="wp-block-preformatted">./temp.sh</pre>

Arka planda çaşıltırmak için:

<pre class="wp-block-preformatted">./temp.sh >&/dev/null &</pre>

Arka planda çalışıp çalışmadaığının kontrolü için 

<pre class="wp-block-preformatted">top | grep python</pre>

yada Influxdb web arayüzünden dataları kontrol edebilirsiniz.

Datalar 5 saniyede bir akmaya devam ederken Grafana&#8217;yı kuralım. Bu işlem için yeniden docker işlemleri için kurduğumuz sunucumuza geçiyoruz ve ilk olarak grafana imajını indiriyoruz ve runlıyoruz.

<pre class="wp-block-preformatted">sudo docker pull grafana/grafana
sudo docker run -d -p 3000:3000 grafana/grafana
sudo docker ps </pre>

&#8220;docker ps &#8221; ile kurulum yapıp ayakta olan konteynerler içerisinde grafanayı kontrol edelim. Kontrolü sağladıktan sonra Grafananın web arayüzüne girelim.

<pre class="wp-block-preformatted">http://ip.adresi:3000</pre>

<ul class="wp-block-gallery columns-1 is-cropped">
  <li class="blocks-gallery-item">
   [![grafana.png](https://i.postimg.cc/wv59pSXh/grafana.png)](https://postimg.cc/473Dv2Fd)
  </li>
</ul>

Default olarak gelen **user: admin parola:admin**

<ul class="wp-block-gallery columns-1 is-cropped">
  <li class="blocks-gallery-item">
   [![sadasd.png](https://i.postimg.cc/5tqrN1rN/sadasd.png)](https://postimg.cc/1gXBvxPL)
  </li>
</ul>

İlk karşımıza çıkan ekranda **&#8220;add data source&#8221;** seçip influxdb&#8217;yi seçiyoruz. Açılan ekrandan URL kısmına influxdb api&#8217;sini (influxdb çalışan sunucunun ip adresi:8086 portu) sonrasında InfluxDB Details&#8211;> Database kısmına database ismini ekleyip kaydedip çıkıyoruz. 

Anasayfaya geri dönüp aynı paneldeki &#8220;**New Dashboard**&#8220;a tıklıyoruz. Çıkan sayfadan &#8220;**New Panel**&#8221; içerisindeki **&#8220;Add Query&#8221;** butonuna tıklıyoruz. Açılan sayfayı aşağıdaki gibi düzenleyip kaydediyoruz. İlk olarak Query kısmından oluşturduğumuz Influxdb&#8217;yi seçmeyi unutmayın(default olandeğil). From kısmında **measurement** kısmına tıklayınca otomatik olarak database&#8217;e yolladığımız temperature key value çıkıyor. Sonrasında field kısmındada yolladığımız değerlerin isimlerini göreceksiniz. Select satırının sonundakı &#8220;+&#8221; işaretinden yeni field ekleyip fazladan tabloya değer ekleyebilirsiniz. Aynı yerden Alias ekleyip bu değerlerin tablodaki isimlerini değiştirebilirsiniz. Bu işlemlerden sonra üsttekki tabloya databasedeki değerlerin geldiğini göreceksiniz.

<ul class="wp-block-gallery columns-1 is-cropped">
  <li class="blocks-gallery-item">
   [![grafpanel.png](https://i.postimg.cc/mrbwj9dB/grafpanel.png)](https://postimg.cc/SnvCNnFv)
  </li>
</ul>

<ul class="wp-block-gallery columns-1 is-cropped">
  <li class="blocks-gallery-item">
   [![grafik.png](https://i.postimg.cc/WzM8S7Pp/grafik.png)](https://postimg.cc/V0kXkn62)
  </li>
</ul>

**SMTP ile Alarm ekleme**

Yazdığımız python koduna bazı eklemeler yaparak sıcaklık ne nem belirli seviyelere yğkseldiğinde veya düştüğünde mail olarak haber vermesini sağlayabiliriz. Bunun için aşağıdaki kodları yazdığımız python koduna ekleyelim.

<pre class="wp-block-preformatted">import smtplib #kodun başına diğer importların arasına ekleyin
def mail(msg):
     mail = smtplib.SMTP("smtp.gmail.com",587) 
     mail.starttls()
     mail.login('sender_mail','sender_mail_password')
     mail.sendmail("sender_mail","destination_mail",msg)
 if temp>=21.0: 
     msg = 'Oda sicakligi ' + str(temp) + '<em>C' + ' Sicaklik yuksek!'     </em>
     <em>mail(msg) </em>
<em>elif temp&lt;=15:     </em>
      <em>msg = 'Oda sicakligi ' + str(temp) + '</em>C' + ' Sicakligi artirmaniz onerilir.'
      mail(msg)
 elif hum >=35:
     msg = 'Odadaki nem orani  %' +  str(hum)  + ' Nem orani cok yuksek!'
     mail(msg)
 elif hum &lt;=15:
     msg = 'Odadaki nem orani  %' +  str(hum)  + ' Nem orani cok dusuk!'
     mail(msg)</pre>
