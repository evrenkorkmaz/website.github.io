---
title: "OSI (Open System Interconnection) modeli ve Katmanları"
date: 2019-11-28T14:00:58+00:00
layout: post
categories:
---


# OSI(Open System Interconnection) modeli ve Katmanları 

# OSI Modeli Nedir?

OSI modeli (Open System Interconnection) 7 kaymanındaki protokolleri uygulamak için bir network frame'i tanımlar.ISO (International Organization for Standardization) tarafından geliştirilen bir standart'tır.
Bu modelin geliştirilmesindeki amaç 2 bilgisayar arasındaki iletişimin nasıl olacağını belirlemektir.
Bilgisayar arası paket iletimi bilindiği üzere physical(fizilsel) olarak yapılır. Bilgisayar gelen elektrik sinyallerini kendine göre anlamlı hale getirir. Fakat daha sonrasinda gelen bu paket içerisindeki sıfırlar ve bir'lerin ne anlama geldiğini anlamak için bu tarz modellere ihtiyacı vardır.
Gelen paketleri anlamlı hale getirmek için sadece OSI kullanılmaz. Kendimizde bir paket üretip bu paketlerle başka bilgisayarlar ile iletişim kurabiliriz. Fakat paketin iletimini yapan router ve switchler, paketi alan bilgisayar vs. bu paketi nasıl anlamdırcağını bilmesi gerekir. 
OSI öncesindeki dönemde, yalnızca bilgisayar donanımı üreten kuruluşlara özgü ağlar vardı. Bu ağların özellikleri, çoğunlukla yalnızca o üreticinin donanımının bağlanmasına izin verecek biçimde tanımlanmıştı. 
Onlardan ayrı olarak OSI, çeşitli üreticilerin ürünlerinin bağlanabileceği bir ağ için, bir sektör etkinliği olarak ortaya çıkmıştır. 
OSI Modeli herhangi bir donanım ya da bilgisayar ağı tipine göre değişiklik göstermemektedir. 
OSI’nin amacı kısaca 2 bilgisayar arasındaki iletişim için gerekli olan protokolleri bir standarta oturtup verimli bir iletişim sağlamak.
OSI bir donanım yada yazılım değildir. Fakat yazılımlar ve donanımlar OSI modeline uygun şekilde üretim yaparlar. Böylece ürünler birbirleri ile uyumlu şekilde haberleşebilir.

OSI 7 Katmandan oluşur.

# Fizilsel (Physical) Katman 

- Fiziksel katman OSI modelinin 1. katmanı olarak bilinir.
- Elektrik,ışık ve radyo sinyalleri alıp veya yollamakla sorumludur.
- RS232, ATM, FDDI gibi protokoller bu katmanda çalışır 

Bu katmanda yollanacak paketteki datalar elektrik sinyaline çevrilip yollanır yada gelen elektrik sinyallerini alıp bilgisayarın anlayabileceği şekle(bitlere) çevrilir.


# Data Link Layer 

- Fiziksel katmana erişmek ve kullanmak ile ilgili kuralları belirler.
- Paket içeriğindeki dataları fiziksel katmana yollamak için bitlere çevirmek yada datalink katmanında bitlere çevrilen sinyalleri yorumlama işlemi yapılır.
- Bu katmanda Ethernet ya da Token Ring olarak bilinen erişim yöntemleri çalışır ve bu erişim yöntemleri verileri kendi protokollerine uygun olarak işleyerek iletirler.
- Data Link katmanının büyük bir bölümü network kartı içerisinde gerçekleşir.
- Veriler Network katmanından fiziksel katmana gönderilirken belirli parçalara bölünür. Bu parçalara "frame" (çerçeve) denir. Frame'ler verilerin belirli bir kontrol içerisinde gönderilmesini sağlar.
- Data Link katmanı network  üzerindeki diğer bilgisayarları tanımlama, kablonun o anda kimin tarafından kullanıldığının tespiti ve fiziksel katmandan gelen verinin hatalara karşı kontrolü görevini yerine getirir.
- Data Link katmanı iki alt bölüme ayrılır:
   - LLC
   - MAC
MAC alt katmanı veriyi hata kontrol kodu (CRC), alıcı ve göndericinin MAC adresleri ile beraber paketler ve  fiziksel katmana gönderir.
Alıcı tarafta MAC alt katmanı bu işlemin tersini yaparak frame'leri  LLC alt katmanına iletir.
LLC ise gelen frame'leri bir üst katmanı olan Network katmanına iletimini sağlar. 
Protokole özel mantıksal portlar oluşturur(Service Access Points, SAPs). Böylece alıcı ve gönderici cihaz aynı protokoller( OSI -> OSI veya TCP/IP -> TCP/IP ) ile iletişime geçebilir.
LLC ayrıca veri paketlerinden bozuk gidenlerin veya karşı taraf için alınanların tekrar gönderilmesinden sorumludur. Flow Control yani alıcının işleyebileğinden fazla veri paketi gönderilerek boğulmasının engellenmesi de LLC’nin görevidir.

Bu katmanda çalışan protokollere örnek:
- ATM
- ARP
- IS-IS
- LLC
- MAC ...

# Network Layer

Network katmanı, yollanacak paketler farklı bir ağa gönderilmesi gerektiğinde, network cihazlarının (router-switch) nereye yönlendireceklerini bilmesi için gerekli olan IP ve MAC adres bilgilerinin eklendiği katmandır.
Bu katmanda datalar paket(package) olarak isimlendirilir.
- Switching ve routing teknolojisi bu katmanda çalışır.
- Router'lar paketi nereye yönlendireceklerini bu layer'a bakarak karar verir.
- İki host arasında virtual circuit adı verilen internet üzerinden nasıl gideceğini belirleyen mantıksal yol (logical path) oluşturulur.

Bu katmanda çalışan protokollere örnek:
- IPv4
- IPv6
- ICMP
- IGMP
- IPsec

# Transport Layer

- Transport layer üst katmanlardan gelen datayı, network paketi boyutlarına böler.
- Bu katmanda datalar segment halinde taşınır ve segment olarak isimlendirilir.
- Taşıma katmanı alt katmanlar (Transport Set) ve üst katmanlar (Application Set) arasında geçit görevini üstlenir.
- Datanın uçtan uca iletimini sağlar ve paketin ulaşıp ulaşmadığını yada zamanında ulaşıp ulaşmadığını kontrol eder kontrol eder.

Bu katmanda çalışan protokollere örnek:
- TCP
- UDP
- SPX

# Session Layer

Session Layer, birden fazla bilgisayar ile aynı anda iletişim kurulmaya çalışıldığında paketlerin doğru bilgisayarlara iletimini sağlar.
- Oturum ve bağlantı koordinasyonu ile ilgilenir.
- Uygulamalar arası bağlantı kurulması, yönetmesi ve bağlantıyı sonlandırmasından sorumludur.
- NetBIOS ve Sockets gibi protokoller farklı bilgisayarlarla aynı anda olan bağlantıları yönetme imkanı sağlarlar.

# Presentation Layer 

Bu katmanın en önemli görevi gönderilecek olan verinin diğer bilgisayara anlaşılacak şekilde çevrilmesidir.
Sizin ekranınıza gelecek olan veriden sorumludur.
Datayı Encryption and decryption edilmesi bu katmadan gerçekleşir
GIF, JPEG, TIFF, EBCDIC, ASCII vb. bu katmanda çalışır.
Bu katman çoğunlukla network ile ilgili olmamakla birlikte kastedilen şey aynı file’ı okuyabilen programlar kullanmaktır.

# Application Layer

Uygulama katmanı, kullanıcıya en yakın olan katmandır.
Bizler bu katmanda çeşitli software’ler kullanarak (Chrome, Firefox, Skype vb.) verilerilerini oluşturular.
Dosya aktarımları, mail ve diğer network yazılımı hizmetleri için uygulama hizmetinden sorumludur.
FTP, HTTP, Telnet gibi protokoller burada çalışır.
