---
title: "DHCP-Snooping"
date: 2020-07-20T14:00:58+00:00
layout: post
categories:
---


# DHCP Snooping 
kısa özet: Ağdaki bir host dhcp server olarak birilerinin dhcp isteklerini yanıtlayıp onlara ip verebilir. Bunun yerine yollanan dhcp boradcast paketlerini ağdaki cihazlara trusted olan porttan iletimi(broadcast olan paketleri unicast yaparak) yollar ve adminin seçtiği ve bildiği dhcp sunucusundan ip alması sağlanır.

DHCP bilindiği üzere ağa katılan hostlara otomatik ip,netmask,gateway gibi ağda kullanması gereken bilgileri yollar.
DHCP sayesinde ağdaki her hosta tek tek static adres tanımlaması ve ip çakışması gibi durumlar engellenir.
Fakat hostlar kendilerine gelen ilk DHCP teklifini kabul eder.
Ağda DHCP sunucu çalıştıran bir kişi DHCP isteğinde bulunan bir hosta kendi istediği bilgileri içeren DHCP OFFER paketi yollayarak bu bilgileri alması sağlanabilir.
Bu sayede isterse bütün networkün kendi cihazları üzerinden geçmesini sağlayarak bütün paketleri izleyebilir.
Ayrıca dhcp sunucu kuran kişi hostun gitmek istediği internet adresleri yerine kendi istediği adrese yönlendirmesini yapabilir.
İstemci güvenliğini ve gizliliğini tehdit eden bu durumu engellemek için Cisco cihazlar üzerinde DHCP araya girme (DHCP Snooping) özelliği geliştirilmiştir.

Ortamda bulunan dhcp sunuculara ait portları trusted olarak işaretleyerek ağda güvenliği sağlar. 
Böylelikle ortama bilerek veya bilmeyerek dahil edilen bir başka dhcp sunucudan kaynaklı sorun ve güvenlik problemi bu şekilde aşılmış olur. 
Ayrıca üzerindeki portlardaki client istemcilerin aldıkları ip adreslerini kendisi bir veritabanında (DHCP Snooping Binding) tutmaya başlar. 
Switch üzerindeki untrusted portlarından gelen DHCP isteklerini (DHCP discovery packet) trusted olarak işaretlediği portlara yönlendirir ve untrusted portlardan gelen DHCP paketlerini yok eder.

# DHCP Snooping Konfigürasyonu

sw1 (config)# ip dhcp snooping		--> DHCP Snooping  aktif hale getirmek için kullanılır.
sw1 (config)# no ip dhcp snooping	--> DHCP Snooping pasif hale getirmek için kullanılır.
sw1 # show ip dhcp snooping		--> Yapılan DHCP konfigürasyonunu doğrulamak için kullanılır.
Sw1 (config)# ip dhcp snooping vlan xx	--> DHCP snooping bir VLAN üzerinde aktive etmek için kullanılır. Pasif hale getirmek için "no ip dhcp snooping vlan xx" komutu kullanılabilir.
sw1 (config-if)# ip dhcp snooping trust	--> Girilen arayüzdeki portta DHCP Offerlara izin vermek için kullanılır.
sw1 (config-if)# no ip dhcp snooping trust -->  Portu untrasted olarak işaretleme yapar. 

# DHCP Snooping Testi
.
[url=https://hizliresim.com/vREx1S][img]https://i.hizliresim.com/vREx1S.png[/img][/url]
.

Resimde  göründüğü gibi ortadaki swithc'e bağlı 2 router, 2 tane de host görüyorsunuz.
Burdaki senaryo ilk başta dhcp snoop üzerinden olacak.
Switchin bütün portlarını örnek için access moda alıp default vlan 1 üzerinden haberleşmesini sağlıcaz.
Sonrasında deneme için router'lardan birini dhcp server olarak konfigüre edicez fakat switch üzerinden herhangi bir ip helper adres veya dhcp snoop özelliğini kullanmıcaz.
Sonrasında hostlardan dhcp isteklerini yollıcaz ve router'dan ip alıp almadığını kontrol edicez.
Ip adreslerini aldıktan wireshark ile paket kontrolü sağladığımızda DHCP OFFER paketleri DHCP Broadcast olarak bütün portlardan iletilmiş fakat 1 tane dhcp server olduğundan sadece o routerdan DHCP OFFER döndüğünü gözlemlicez.
Şimdi sıkıntı burda başlıyor. Ağa katılan birisi kendini dhcp server olarak kullanabilir mi? Evet kullanabilir.
Çünkü DHCP DISCOVERY paketleri broadcast olarak bütün portlardan iletiliyor. Aynı anda iki serverdan da cevap dönme imkanı var. Eğerki sonradan ağa katılan kötü niyetli dhcp server bizim server'ımızdan önce cevap döndürürse host ilk gelen offer paketini kabul edecek olup yanlış bilgileri alır.
Bunun önüne geçebilmek için Switch üzerindeki "dhcp snooping" özelliğini aktif etmemiz gerekiyor. 
Dhcp snooping özelliği sayesinde güvenilir olan dhcp server'a bağlı olan portu "trusted port" olarak tanıtarak gelen dhcp offer isteklerinden sadece trusted porttan gelenlerin iletimini sağlayabiliriz.

# Router R1 için dhcp server konfigrasyonu.

İlgili interface'e ip ataması yapmamız gerekiyor.
R1(config)#int f0/0
R1(config-if)#ip address 192.168.10.1 255.255.255.0
R1(config-if)#no shutdown
R1(config-if)#exit

# DHCP yapılandırması
R1(config)#ip dhcp pool dhcp-vlan1
R1(dhcp-config)#network 192.168.10.0 255.255.255.0 
R1(dhcp-config)#default-router 192.168.10.1       
R1(dhcp-config)#exit                          

Default router komutu ile hostlara gönderilen gateway adresi olarak kendi adresini göndermiş oluyoruz.

Topoloji bu durumda iken switch üzerinde bağlı olan portlar da aktif ise hostlardan dhcp isteğinde bulunarak test edebiliriz.

 PC-1> ip dhcp
DDORA IP 192.168.10.2/24 GW 192.168.10.1

PC-1> show ip

NAME        : PC-1[1]
IP/MASK     : 192.168.10.2/24
GATEWAY     : 192.168.10.1
DNS         : 
DHCP SERVER : 192.168.10.1
DHCP LEASE  : 86395, 86400/43200/75600
MAC         : 00:50:79:66:68:00
LPORT       : 10002
RHOST:PORT  : 127.0.0.1:10003
MTU         : 1500

Göründüğü üzere client gelen dhcp offerı kabul etti.
Diğer router R2 içinde aynı dhcp yapılandırmasını yapalım ama bu kez hangi cihazdan ip aldığını gözlemlemek için farklı networkler için konfigüre edelim.
R1 için dhcp pool 192.168.10.0/26 (mask 255.255.255.192)
R2 için dhcp pool 192.168.10.64/26 olsun.

R2 için: 
R2#configure terminal 
R2(config)#interface fastEthernet 0/0
R2(config-if)#ip address 192.168.10.65 255.255.255.192


# R2(config-if)#ip address 192.168.10.65 255.255.255.192
# R2(config-if)#no shutdown 
# R2(config-if)#exit 
# R2(config)#ip dhcp pool dhcp-vlan1
# R2(dhcp-config)#network 192.168.10.64 255.255.255.192
# R2(dhcp-config)#default-router 192.168.10.65


[url=https://hizliresim.com/ynbf1L][img]https://i.hizliresim.com/ynbf1L.png[/img][/url]

2 ayrı hosttanda dhcp isteklerinde bulunuyoruz. Göründüğü gibi farklı dhcp serverlardan gelen istekler var fakat ilk geleni kabul ediyor.

[url=https://hizliresim.com/CM9IBD][img]https://i.hizliresim.com/CM9IBD.png[/img][/url]

Wiresharktan paket incelemesi yaptığımızda 2 serverdanda DHCP Offer paketlerinin çıktığını gözlemleyebiliriz.

# DHCP Snooping Konfigürasyonu

Şimdi yaptığımız topoloji üzerinde bulunan switch'e bir dhcp sunucusundan gelen istekleri kabul etmesi için bu portlardan birini Trusted port olarak işaretliyoruz.
Önce dhcp snooping özelliğinin durumunu kontrol edelim. 

# EWS1#show ip dhcp snooping
Switch DHCP snooping is disabled
Switch DHCP gleaning is disabled

vlan 1 için dhcp snoop özelliğini aktif hale getirelim
# EWS1#ip dhcp snooping vlan 1

# EWS1#show ip dhcp snooping 
Switch DHCP snooping is enabled
Switch DHCP gleaning is disabled

Topoloji bu durumdayken hostlardan hiç biri ip adresi almaz. 
Dhcp snoop özelliği aktif ama trusted port olmadığıdan default olarak  bütün portlar untrusted olur ve  switch hiç bir dhcp paketini yönlendirmez.
Bu yüzden portlardan birini trusted port olarak yapılandırıyoruz.
# EWS1#config t
# EWS1(config)#int fa 0/0
# EWS1(config-if)#ip dhcp snooping trust

Sonrasında hangi vlanların bu trusted porta bağlı dhcp sunucusundan ip alabileceğinin bilgisini giriyoruz.

# EWS1(config)#ip dhcp snooping vlan 1

Sonrasında yeniden clientlara dhcp istekleri yaptırarak ikisininde sadece trusted porta bağlı sunucudan ip aldığını gözlemleyebiliriz.
