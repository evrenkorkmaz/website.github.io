---
title: "DHCP - DHCP Relay Agent Nedir?"
date: 2019-10-29T14:00:58+00:00
layout: post
categories:
---


# DHCP nedir?

DHCP (Dynamic Host Configuration Protocol), bir ağa bağlanmak isteyen cihazların dinamik konfigürasyonunu yapılmasını sağlar.
Cihazların (Bilgisayar- telefon vs.) bağlandığı ağ üzerinde yolunu bulabilmesi için gerekli olan bilgileri DHCP tarafından sağlanır.
DHCP sayesinde ağa bağlanan cihazlara; DNS sunucu adresi, Default Gateway, ip adresi ve proxy sunucu bilgileri (varsa) gönderilir.
Örneğin ev veya iş yerindeki modem/router ,ağa dahil olan cihazlara birer ip adresi atar ve ağda ip adreslerinin çakışmasını engeller. DNS sunucu adresi ve default Gateway olarak genelde kendi adresini gönderir. Bu sayede cihaz internete cıkacağı zaman modem/router'a ulaşması gerektğini bilir.

DHCP client/server mimarisi ile çalışır. Client/server arasındaki paketler UDP ile taşınır.
Client ağa ilk kez dahil olduğunda bir "DHCP Discovery" paketini ağa gönderir.
Bu paketin amacı DHCP server'i bulmaktır.
DHCP Discovery paketini alan DHCP Server, paketi gönderen bilgisayara uygun bir ip adresi ve bu adresi ne kadar süre kullanabileceğinin(lease Time) içeren bir DHCP offer (DHCP Teklifi) gönderir.
Client genellikle DHCP Offer içerisideki ip adresini ve Lease Time'ı kabul ederek sunucuya DHCP Request gönderir.
Son olarak server, client'a  eğer uygunsa DHCP Request paketinde bulunan ip adresini tahsis eder. 
Client, verilen ip adresini Lease Time süresince yenilemez ise bu ip başka bir cihaza verilebilir.
Bu nedenle client, Lease Time süresinin yarısı dolduğunda, DHCP server'a yenileme talebi yollar.
Çok özel bir durum olmadıkça server Lease Time'ı uzatır ve client'in ip adresini korumuş olur.

Bazı özel durumlar olabilir. Örneğin bir web sunucumuzun sürekli aynı ip adresi almasını istediğimiz bir durum olabilir.
Böyle durumlarda DHCP server üzerinde, web sunucumuzun mac adresi ile ip adresi eşlemesi yapabiliriz. Böylece web sunucusundan gönderilen DHCP Discovery paketlerine mac adresi ile eşleşen ip adresini DHCP offer olarak sunar.

# DHCP Server Konfigürasyonu 

Bir ağda DHCP sunucu olarak kullanılacak bir çok cihaz var. Router'ları bile DHCP Server olarak konfigüre edebiliriz. 
Fakat bu cihazları kendi amaçları dışında ekstra özellikleri ile kullanmak, bu cihazların performansını etkileyebileceğinden en mantıklısı sadece DHCP server olarak kullanılacak bir cihaz. 
Yapacağımız projede Vlan 10 (192.168.10.0/24) ve vlan 20 (192.168.20.0/24) networklerimiz olacak. Bu networkler dışında DHCP sunucumuz içinde ayrı bir network oluşturucaz. Bu networkte Vlan 30 (192.168.30.0/24) olacak.
DHCP servere Vlan 30 networküne ait bir static ip tanımlaması yapıcaz. Daha sonra DHCP sunucuya bu static ip verdiğimiz ethernet kartını dinlemesi için gerekli konfigürasyonu yapıcaz.
Sonrasında dhcp.conf dosyasında vlan10 ve Vlan20 için DHCP sunucdan alacakları ip aralığı,default gateway ve broadcast adreslerini tanımlayacaz. 
Topoloji içerisince DHCP sunucumuzun bağlı olduğu portu verdiğimiz vlan'a access yapıp sonrasında networklerin toplandığı cihaza (Duruma göre Switch yada router) helper-address ekleyerek DHCPDISCOVERY paketlerini DHCP sunucuma yönlendirmesini sağlayabiliriz.

İlk olarak DHCP server olarak kullanacağımız Ubuntu 18.04 makinemizin konfigürasyonu ile başlayalım. 
Makinemizi update'leyip dhcp server için gerekli servisi indirelim.
# sudo apt-get update 
# sudo apt install isc-dhcp-server

# dhcp.conf yapılandırması
DHCP yapılandırması için /etc/dhcp/dhcpd.conf dosyasını konfigüre etmemiz gerekli
# sudo nano /etc/dhcp/dhcpd.conf
dhcp.conf rehber olarak kullanılabilecek şekilde hazırlanmış.
Yorum satırında olan yada aktif halde olan komutlar görebilirsiniz.
Örnek olarak:

# ddns-update-style none;
# option domain-name-servers 192.168.1.20;
# default-lease-time 7200;
# max-lease-time 600;
# authoritative;
Tek tek içneliyecek olursak;

#authoritative;
Bu satır; DHCP sunucusu üzerindeki ip aralığı dışında veya yanlış ip alma durumunda gönderilen "DHCPNAK" paketini yollar. Eğer yorum satırında ise yollamaz.

# ddns-update-style none;
Eğer ki networkünüzde bir sunucunun IP Adresi değişmiş ise DHCP sunucunuza DNS sunucu adresini güncellemesini bu komut ile söyleyebilirsiniz. Ancak networklerde sunucu IP’leri genelde statik olarak atanır ve değişmemesi sağlanır bu yüzden bu komutun yanına “none” getiriyoruz.

# option domain-name-servers x.x.x.x
Ağa katılan cihazlara hangi DNS sunucusunu kullanacaklarının bilgisini atayabiliriz.
Virgül ile birden fazla DNS tanımlayabiliriz.

# default-lease-time 7200;
# max-lease-time 600;
İsimlerindende anlaşılacağı gibi lease time sürelerinin tanımlanması.

Sonasında ip yapılandırması için aşağıdakine benzer bir fonksiyonları dosyanın sonuna ekliyoruz.
.   
subnet 192.168.10.0 netmask 255.255.255.0 {
range 192.168.10.150 192.168.10.170;
option subnet-mask 255.255.255.0;
option routers 192.168.10.1;
option broadcast-address 192.168.10.255;
default-lease-time 600;
max-lease-time 7200;
}

subnet 192.168.20.0 netmask 255.255.255.0 {
range 192.168.20.150 192.168.20.170;
option subnet-mask 255.255.255.0;
option routers 192.168.20.1;
option broadcast-address 192.168.20.255;
default-lease-time 600; 
max-lease-time 7200;
}

subnet 192.168.30.0 netmask 255.255.255.0 { }
 . 
Satır satır inceliyecek olursak;

# subnet 192.168.0.0 netmask 255.255.255.0 {
Burada ip atamasi yapmasını istediğimiz netwok bilgisini ve subnet-mask tanımlamasını yapıyoruz.

# range 192.168.0.150 192.168.0.170;
DHCP ile verebileceğimiz ip adresles aralığını tanımlıyoruz. 
Bu tanımlamanın sebebi özel durumlarda static olarak ip tanımlaması yapılan cihazlarla ip çakışmaması için duruma göre bir aralık tanımlama imkanımız var.

# option subnet-mask 255.255.255.0;
Cihaz tarafından alınacak subnet-mask bilgisi

# option routers 192.168.0.1;
router adresi yani gateway bilgisi

# option broadcast-address 192.168.0.255;
broadcast adresi

#default-lease-time 600;
#max-lease-time 7200;
DHCP sunucusu tarafından verilen ip'nin ne kadar süre boyunca ayıracağının bilgisi
Bu tanımlamayı her fonksiyonun içerisine tek tek yazmak yerine en üste bir kere tanımlayarak hepsinde aynı lease-time tanımlaması yapabiliriz.
Yukarıdada bahsettiğim gibi bu sürenin yarısında Client'tan süreyi uzatma isteği gelmez ise atanan ip başka bir Client'a yönlendirilebilir.

# subnet 192.168.30.0 netmask 255.255.255.0 { }
İçi boş olan bu fonksiyonun amacı; 
Yukarıdada belittiğim gibi DHCP sunucu İçinde olmadığı networklere ip ataması yapıcak.
Kurduğumuz DHCP servisinin çalışma mantığından dolayı kendi networkünden gelecek paketleride dinlemek zorunda.
. 
Bu konfigürasyonlardan sonra kaydedip çıkıyoruz.

# sudo vim /etc/default/isc-dhcp-server
Bu dosya içerisine DHCP sunucusuna gelecek isteklerin hangi ethernet kartından olabileceğinin bilgisini girmemiz gerekiyor.
# INTERFACES="eth0"  gibi
Çoklu tanımlama için :
# INTERFACES="eth0 eth1 eth1.20"
yapabiliriz. Fakat bu projede bu işi tek bir ethernet kartını dinleyerek yapıcaz. Çoklu şekildede yapmak mümkün fakat büyük ağlarda her network için ayrı static ip vermemiz gerekmektedir. 
Bu işlemler zor olduğundan ip vermemiz gereken networkler dışında kalan bir ağada DHCP sunucumuza ip verip bu işi halledebiliriz.
Bazı cihazlarda yada versiyonlarda bu dosya içerisinde INTERFACESv4-INTERFACESv6 şeklinde çıkabilir. Bu durumda IPv4 ataması yacağımızdan aynı tanımlamayı INTERFACESv4 içerisine yapabiliriz.


Bir sonraki adım : 
# DHCP server'e static ip tanımlaması
# sudo nano /etc/network/interfaces

auto enp0s3
iface enp0s3 inet static
 address 192.168.30.2
 netmask 255.255.255.0
 gateway 192.168.30.1
 broadcast 192.168.30.255


Konfigürasyon sonrası static ip'yi yenilemesi için 
# service networking restart 

# ip a 
komutu ile kontrol edin.

Sonrasında dhcp servisini yeniden başlatalım.
# sudo systemctl restart isc-dhcp-server.service
# sudo systemctl status isc-dhcp-server.service

Servisimizin çalışır halde olması gerekiyor. Eğer bir hata var ise yüksek ihitmal  dhcp.conf dosyasındadır. Yazım yanlışı olup olmadığına bakın.

Servisi denemek için GNS3'te bir proje oluşturabilirsiniz. GNS3'ün içerisinde olan basit clientler(VPCS) ile bir switch ile dhcp kurulumu yapabilirsiniz.
VPCS üzerinde "dhcp" komutu ile DHCPACK paketi yollayıp ip alabilirsiniz.



# DHCP Relay Agent 
Kurumlar içerisindeki network altyapılarının büyümesi ve gelişmesinden dolayı, DHCP server'lerin farklı subnetteki networklere ip ataması yapması mecburiyet haline geldi.
Bu durumlarda devreye DHCP Relay Agent devreye giriyor.
DHCP Relay Agent, farklı subnetteki DHCP Server'dan kendi subnetimizdeki kullanıcılara ip aldırmamızı sağlar.
DHCP Relay Agent  subnetindeki kullanıcıların DHCP Broadcast'lerini yakalayarak bunları Unicast'e çevirip ilgili DHCP Server'a gönderir.
DHCP Relay olara kullanılacak cihaz, routerda olarbilir bir bilgisayarda olabilir.
 DHCP Server Konfigürasyonu başlığı altında anlattığım projede Gelen paketleri DHCP suncusuna yönlendirmek için Cisco Switch kullandım.
Gelen paketleri yonlendirmel için:
>enable
#Configure terminal
S1(config)# interface vlan 10 
S1(config-if)# ip helper-address 192.168.30.2
S1(config-if)# no shutdown
S1(config-if)#exit
S1(config)# service dhcp

# ip helper-address x.x.x.x
Bu komut gelen DHCPDISCOVERY paketlerini ip'sini tanımladığımız adrese yönlendirir.
Bu adres direk dhcp server'in statip ip adresi yada başka bir ağda ise o ağın ip'si olabilir


# service dhcp
Bu komut switch üzerindeki dhcp server ve dhcp relay agent özelliğini aktif eder.
service dhcp aktif değilse, gelen paketlerin broadcast adreslerini 0.0.0.0 yaparak yönlendirmeyi yapar. Bu yüzden DHCP server isteğin hangi networkten geldiğini anlayamaz.

