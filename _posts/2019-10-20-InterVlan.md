---

title: InterVlan Routing 
date: 2019-10-20T21:04:00+00:00

layout: post

categories:

---


# InterVlan Routing  nedir? 

InterVlan routing bir vlandan başka bir vlana olan trafiğin router ile yinlendirilmesini sağlamaktır. Kısacası vlanlar arası yönlendirme(routing).
Inter vlan iki şekilde yapılıyor. 
- Traditional InterVlan Routing
- Router On a Stick

# Traditional InterVlan Routing

Bu Intervlan türünde router ve switch üzerinde her vlan için interface açılması gerekiyor.
Router tarafında bu interface'leri açıp o portu kullanacak vlan'ın ip adresi ve subnet bilgisini girmek yeterli.
Switch tarafında ise önce bu vlanları oluşturup router ve client'e bağlı olan portlarını access mode yapmamız yeterli.

# Tradidional InterVlan Konfigürasyonu
Traditional InterVlan'ı denek için basit bir proje oluşturdum.
Bu projede 2 tane vlan olacak:
Vlan10 - 192.168.10.0/24
Vlan20 - 192.168.20.0/24
Bu vlanlar switch ve routerda topolojidede göründüğü gibi 2 farklı interface ile bağlı.

[![topoloji.png](https://i.postimg.cc/CL27G2Qr/topoloji.png)](https://postimg.cc/hf9Vg2gV)

# Router Konfigürasyonu

Yukarıdada bahsettiğim gibi bağlı olan iki interface'de 2 vlan için farklı şekilde configüre edilecek.
fastEthernet 0/0 vlan10 için 
fastEthernet 0/1 vlan 20 için konfigüre edilecek.
 
R1#configure terminal 
R1(config)#interface fastEthernet 0/0
R1(config-if)#ip address 192.168.10.1 255.255.255.0  
R1(config-if)#no shutdown 
R1(config-if)#exit 
R1(config)#interface fastEthernet 0/1
R1(config-if)#ip address 192.168.20.1 255.255.255.0
R1(config-if)#no shutdown 
R1(config-if)#exit
R1(config)#do wr
Building configuration...
[OK]

# Switch Konfirasyonu.
Switch konfigürasyonu için ilk olarak Vlanları oluşturuyoruz.
Sonrasında bu vlanlar için kullanacağımız portları access yapıp vlanları tanımlıyoruz.

ESW1#vlan database 
ESW1(vlan)#vlan 10
ESW1(vlan)#vlan 20
APPLY completed.
Exiting....
ESW1#configure terminal 
ESW1(config)#interface fastEthernet 0/0
ESW1(config-if)#switchport mode access 
ESW1(config-if)#switchport access vlan 10
ESW1(config-if)#no shutdown 
ESW1(config-if)#exit
ESW1(config)#interface fastEthernet 0/2
ESW1(config-if)#switchport mode access 
ESW1(config-if)#switchport access vlan 10
ESW1(config-if)#no shutdown 
ESW1(config-if)#exit
ESW1(config)#interface fastEthernet 0/1
ESW1(config-if)#switchport mode access 
ESW1(config-if)#switchport access vlan 20
ESW1(config-if)#no shutdown 
ESW1(config-if)#exit
ESW1(config)#interface fastEthernet 0/3
ESW1(config-if)#switchport mode access 
ESW1(config-if)#switchport access vlan 20
ESW1(config-if)#no shutdown 
ESW1(config-if)#exit
ESW1(config)#do wr
Building configuration...
[OK]

Konfigürasyonlar bittikten sonra client'lerime ip ve gateway atıyorum. Ardından ping atıyorum.
Atadığım gateway, router konfigürasyonunda interfacelere verdiğim ipler.
.
[![ipatama.png](https://i.postimg.cc/wjkk6YdV/ipatama.png)](https://postimg.cc/Xrp58hqG)
[![ping.png](https://i.postimg.cc/C56WrncR/ping.png)](https://postimg.cc/m1CXtkf4)
Ping atarken wireshark paketlerime bakıp inceleme yapıyorum.

[![wire.png](https://i.postimg.cc/KzYfpG4V/wire.png)](https://postimg.cc/94vyrjdt)

Wireshark üzerinde de görüldüğü gibi aynı switch üzerinde bağlı iki vlanın haberleşmesi sorunsuz çalışıyor.
Wireshark'dan paketleride incelicek olursak;
İlk olarak pingi atan client (PC1) ARP paketi yolluyor. Bu paket ulaşmak istediği networke değil, networke ulaşması için gateway adresine yapıyor.
Aynı ağda olduğunu anladıktan sonra paketi gateway üzerinden farklı bir networke yönlendirmeye çalışıyor.
Router'a kadar gelen paket, kendi üzerindeki network olduğundan dolayı paketi ilgili interface'e yönlendiriyor.
Böylece ping işlemi düzgün bir şekilde çalışmasını yapıyor.


Traditional inter-Vlan Routing ile her vlan için hem Switch hem de Router üzerinde fazladan interface harcamış olduk. 
Switchler için fazladan kullanılan interface pek sorun olmasa da Routerlar için aynı şeyi söyleyemeyiz. 
Zaten oldukça az sayıda interface’i olan Routerlar için her bir interface değerlidir. 
Bu sorunu "Router on a stick" ile çözebiliriz.


# Router on a stick 
Yukarıda da anlattığım gibi Traditional InterVlan'da her vlan için interface yapılandırmamız gerekiyordu.
Router on a stick'te bu işlemi sadece bir interface üzerinden halledebiliyoruz. 
Router üzerindeki bir interface'i sub-interfacelere bölerek bu işlemi sağlayabiliyoruz.
Örnek olarak vlan10 ve vlan20 şeklinde vlanlarımızın olduğu bir ağda router üzerindeki Fastethernet 0/0 interface'i kullanıcaksak:
vlan 10 için "router(config)# interface fastEthernet 0/0.10" şeklinde bu interface'e giriş yapabiliriz.
Açılan interface 0/0.10 olmak zorunda değil. Sadece kullanım kolaylığı ve hatırlanması açısından vlan numarası ile aynı isimde interface kullanmak daha mantıklı.
Interface vlan numarası ile aynı olmak zorunda değil ama aşağıdaki basit topoloji üzerinde yapacağımızda, encapsulation işleminde vereceğimiz numara vlan ile aynı olmalıdır.
Çünkü router gelen paketleri üzerlerinde bulunan IEEE 802.1Q ile encapsulation edilmiş vlan tag'lerinde bakarak ilgili sub-interface'e yönlendirmesini yapacak.

# Router on a Stick Konfigürasyonu

[![topolgy.png](https://i.postimg.cc/Bn15wDjF/topolgy.png)](https://postimg.cc/4YshYYb4)

# Router Konfigürasyonu:

Router ile switch arasındaki port açılır
R2#configure terminal 
R2(config)#interface fastEthernet 0/0
R2(config-if)#no shutdown 
R2(config-if)#exit 

Vlan10 için 0/0.10 interface'i konfigüre edilir.
Encapsulation işlemi vlan id 10 olarak tanımlanır.
Interface'in ip adresi, gateway olan Vlan10 networkne ait 192.168.10.1'i tanımlıyoruz.
R2(config)# interface fastEthernet 0/0.10
R2(config-subif)#encapsulation dot1Q 10
R2(config-subif)#ip address 192.168.10.1 255.255.255.0
R2(config-subif)#no shutdown 
R2(config-subif)#exit

Aynı şekilde 0/0.20 interface konfigürasyonu.
R2(config)#interface fastEthernet 0/0.20
R2(config-subif)#encapsulation dot1Q 20
R2(config-subif)#ip address 192.168.20.1 255.255.255.0
R2(config-subif)#no shutdown 
R2(config-subif)#exit

# Switch Konfigürasyonu:

Switch'in routera bağlı olan interface'den birden fazla vlan kullanacağından trunk moda alıyoruz.
ESW1#configure terminal 
ESW1(config)#interface fastEthernet 0/0
ESW1(config-if)#switchport mode trunk 
ESW1(config-if)#exit 

Vlanları oluşturup, clientlara bağlı portları yapılandırıyoruz.
ESW1#vlan database 
ESW1(vlan)#vlan 10 
VLAN 10 added:
    Name: VLAN0010
ESW1(vlan)#vlan 20
VLAN 20 added:
    Name: VLAN0020
ESW1(vlan)#exit
APPLY completed.
Exiting....
ESW1#configure terminal 
ESW1(config)#interface fastEthernet 0/1
ESW1(config-if)#switchport mode access 
ESW1(config-if)#switchport access vlan 10
ESW1(config-if)#no shutdown 
ESW1(config-if)#exit
ESW1(config)#interface fastEthernet 
ESW1(config)#interface fastEthernet 0/2
ESW1(config-if)#switchport mode access 
ESW1(config-if)#switchport access vlan 20
ESW1(config-if)#no shutdown 
ESW1(config-if)#exit

Konfigürasyondan sonra clientlere ip ataması yapıp ping atıyoruz.

[![ipatama.png](https://i.postimg.cc/wjkk6YdV/ipatama.png)](https://postimg.cc/Xrp58hqG)

[![wireshrk.png](https://i.postimg.cc/PJWMPP7C/wireshrk.png)](https://postimg.cc/0KNS42Pv)

Wireshark üzerinden paketleri incelemesini yapalım.
Alt kısımda göreceğiniz gibi paketler 802.1Q ile vlan id'si eklenerek taglaniyor.
