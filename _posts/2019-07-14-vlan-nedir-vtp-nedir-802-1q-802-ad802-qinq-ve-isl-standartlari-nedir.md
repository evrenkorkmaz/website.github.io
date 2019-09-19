---
title: VLAN nedir? VTP ve GVRP Nedir? 802.1Q, 802.ad(802.QinQ) ve ISL Standartları Nedir?
date: 2019-07-14T21:04:00+00:00
layout: post
categories:
---

# VLAN nedir? VTP ve GVRP Nedir? 802.1Q, 802.ad(802.QinQ) ve ISL Standartları Nedir?


#### VLAN nedir?

VLAN (Virtual LAN) yerel ağı sanal olarak gruplara bölme ve bu grupları yönetmemizi sağlayan yapıdır.

#### VLAN&#8217;a neden ihtiyaç duyuldu?

[![VLAN-768x468.jpg](https://i.postimg.cc/CKZ4X7sH/VLAN-768x468.jpg)](https://postimg.cc/cKSY8Qjr)

Yukarıdaki resimde de görüldüğü üzere çok fazla cihazımız var. Bu cihazların her biri aynı broadcast ile internete çıkmaya çalışıyor yada birbirleriyle olan iletişimlerinde ARP protokolü çalıştığında ağdaki bütün cihazlara istek yolluyor ve bütün ağı meşgul ediyor. Kısacası ağdaki **broadcast trafiğini azaltmak** ve **bant genişliğini arttırmak** için kullanılır. 

VLAN&#8217;a ihtiyaç duyduğumuz bir diğer neden ise **güvenlik**. Birbiriyle ilgisi olmayan ağları birbiriyle iletişim kurmasını engelleyerek dışarıdan gelecek olası bir tehdit engellenmiş olur. Örneğin dışarıdan misafir olarak şirket ağına katılan birisi ağdaki trafiği dinleyebilir ve bilgi ele geçirebilir. Bu nedenle misafir olarak gelen cihazlar, şirket ağından izole bir ağa aktarılır ve sadece kendi içerisinde bulunduğu ağ üzerinde işlem yapabilir.

Son olarak **ölçeklenebilirlik**. VLAN sayesinde switch üzerinde fiziksel konumu neresi olursa olsun kullanıcıyı istediğimiz VLAN grubuna dahil edebiliriz. Sürekli büyüyen bir ağda yeni VLAN grupları oluşturabilir ve cihazları bu gruplara yönlendirme işlemi yapabiliriz. 

Farklı VLAN&#8217;daki cihazlar aynı switch&#8217;e bağlı olsalar bile broadcast adresleri farklı oldukları için birbirleriyle MAC adresleri ile iletişim sağlayamazlar. Haberleşmenin sağlanması için bir üst katmandaki router ile ip adresleriyle iletişim kurarlar. Bir diğer yol ise Layer3 switch kullanmak. Layer3 ve Layer2 switchlerin farkı, L2 switchler OSI Layer2(Data Link) bilgilerini kullanır(MAC adresi). Fakat L3 switchler Layer3 (Network Layer) bilgilerini kullanıp(IP adresleri) haberleşme sağlayabilir.

#### VLAN türleri

#### Data (Veri) VLAN

Bir diğer adı User VLAN(Kullanıcı VLAN&#8217;ı). Standart veri taşıma üzerinde yapılandırılmış VLAN&#8217;dır. Örnek olarak kullanıcıların internet trafiği ses ve video temelli trafiği taşır.

#### Default (Varsayılan) VLAN 

Switch başlatıldığında bütün bağlantı noktaları default olarak bu VLAN&#8217;a toplanır. Bu sayede switch başlatıldığında bütün bağlı cihazlar aynı broadcast trafiğini kullanır ve birbirleriyle iletişim kurmasına olanak sağlar.

#### Management ( Yönetim ) VLAN

Swicth&#8217;i yönetmek için tanımlanan VLAN&#8217;dır. Bu VLAN&#8217;a ip adresi ve subnet mask atandığında SSH gibi uzaktan erişim sağlayan protokollerle bağlanılabilir. Eğer bu VLAN için herhangi özel bir VLAN tanımlanmamış ise default olarak VLAN1 yönetim VLAN&#8217;ıdır. Bu nedenle güvenlik için VLAN1 yönetim vlanı olarak kullanılmaz.

#### Native (Yerel) VLAN

**Trunk** bağlantı noktası atanmış VLAN&#8217;dır. Trunk bağlantı noktası&nbsp;herhangi bir VLAN&#8217;dan gelmeyen trafiği Yerel VLAN&#8217;a yönlendirir. Herhangi bir cihazdan gelen ama herhangi bir VLAN&#8217;a dahil olmayan trafik switch üzerindeki yerel vlan üzerinden iletilir.

#### Private VLAN

İç ağda veya aynı networkte bulunmak büyük güvenlik açıklarına sebebiyet verebilir çünkü Layer2 katman içerisinde bizi gelecek tehtidlerden koruyacak bir firewall vs. mevcut değil. Bu yüzden günümüzde güvenlik konusunda Layer2 katman büyük öneme sahip. Bu güvenlik açıklarını engellemek için Private VLAN kullanabiliriz.

Private VLAN, Vlanlar içerisinde başka Vlanlar oluşturmaktır. Yani aynı Subnete tanımlı iki ucu birbirinden izole etmektir.

**Neden herkese farklı Vlanlar atamak Yerinde Private Vlan oluşturuyoruz.**

Günümüz sistemlerinde 802.1Q ile en fazla 4096 tane Vlan tanımlayabiliriz ve bunların herbiri için ayrı subnet aralığı tanımlamamız gerekiyor. Bu yüzden Vlan sayısı yetse bile ip adreslemede sorunlar çıkabilir. Bu durum kısacası ip adreslemenin verimsiz kullanımına sebebiyet verir. Private Vlan ekstra subnet aralığı bermeden bu işi halletmemize olanak sağlar.

En dışta bulunan Vlan&#8217;a yani içerisine Private Vlanlar oluşturacağımız Vlan&#8217;a Primary Vlan diyoruz.

Private Vlan içerisinde 3 grup port tanımlaması mevcut.

#### Promiscous

Aynı Vlan içerisindeki bütün portlar ile veri alış-verişi yapabilen porttur.

#### Isolated

Bu portlar kendi aralarında direk haberleşemezler. Sadece Promiscous portlar sayesinde iletişim kurarlar.

#### Community

Kendi aralarında ve Promiscous portlarla iletişim kurar. Aynı Primary Vlan altındaki farklı id&#8217;ye sahip Community portlar direk haberleşemezler. Yani her Community kendi arasında haberleşebilir.

#### VLAN Trunkıng Protocol (VTP)

**Trunk** bağlantı noktaları çok sayıda Switch bulunan ağlarda Trunk portlar sayesinde VLAN&#8217;ların bağlantısı sağlanır. Cisco tarafından geliştirilmiş bir protokoldür bu yüzden sadece Cisco cihazlar arasında çalışır.

VTP ise VLAN Domain olarak adlandırılabilir. VLAN&#8217;lar üzerinde VTP ile ekleme,silme,isim değiştirme gibi çeşitli yetkiler verilir ve bu değişiklikler VTP ile diğer switchlere yollanır.

VTP sayesinde her switch üzerinde tek tek değişiklik yapmak ve bu değişiklikler yapılırken oluşacak hatalarla tek tek uğraşmak yerine tek bir switch üzerinden yapılan konfigürasyonların ağda bağlı bütün switchlere aktarmamızı sağlar.

VTP ile çalışmak istediğimiz ağda önce VTP alanı oluşturulur. Aynı alanda olan switchler VTP paket alışverişinde bulunur. Alan oluşturmak için switchte VTP Domain Name (VTP Alan adı) girilir. VTP alanı temelde boş olarak gelir. Aynı alan adına sahip switchler aynı alan içerisinde demektir. İstersek parola tanımlayabiliriz fakat aynı alan adındaki cihazlara aynı parolalar verilmelidir. 

#### VTP Modları

#### Server (Sunucu) Mod

VLAN&#8217;lar üzerinde tüm değişiklikleri yapma yetkisi olan mod. VTP ile çalışılan ağda tüm makinelerde konfigürasyon yapmak için server modunda çalışan bir switche ihtiyaç vardır. Server üzerinde yapılan değişiklikler VTP alan adı sayesinde diğer cihazlara duyurulur. Bu yapılan değişiklikler NVRAM (non-Volatile Memory &#8211; Geçici olamayan bellek) üzerinde saklanır.

#### Clıent ( İstemci ) Mod

VTP ağında değişiklik yapma yetkisi yoktur. Server modunda çalışan switchten yapılandırmaları alıp başka cihazlara iletir. Bu modda yapılan değişiklikler kalıcı değildir, NVRAM de saklanmaz.

#### Tranparent (Şeffaf) Mod

Bu modda çalışan switchler Server gibidir. Üzerilerinde VLAN oluşturabilir,silebilir veya isim değişikliği yapabilir. Server modunda olduğu gibi bu değişiklikleri diğer cihazlara yollar fakat diğer cihazlar bu değişiklikleri yapmaz. Bu modda yapılan değişiklikler sadece kendi üzerinde geçerlidir ve NVRAM üzerinde saklanır. 

#### VTP Configuration Revision Number ( VTP Konfigürasyon Revizyon Numarası) 

Switch üzerinde yapılan değişiklikleri başka cihazlara aktarmak için revizyon numarası büyük önem taşır. Yapılan değişikliklerden sonra VTP ile tüm cihazlarda aktarmak istersek revizyon numarasını 1 arttırmamız gereklidir. Diğer makineler kendi kullandıkları revizyon numarasından büyük ise bu değişiklikleri yapar. 

**Dikkatli olunması gereken bir konudur.** Eğer VTP Domain Name ve parolası aynı bir cihaz Server modunda ağa bağlandığında revizyon numarası olması gerekenden yüksek ise diğer cihazları da günceller.

#### Revizyon Numarası Sıfırlama

Üzerinde değişiklik yaptığımız cihazın revizyon numarasını sıfırlamanın birkaç yolu var.

  * VTP ismini değiştirmek.
  * Flash&#8217;ta tutulan vlan.dat dosyasını silmek.
  * VTP modunu şeffaf moda almak.

#### VTP Advertisement (VTP Bildirimleri)

#### Summary Advertisement (Özet Bildirim)

VTP alan adı, güncel revizyon numarası ve diğer VTP konfigürasyon detaylarını içerir. 5 dakikada bir komşu cihazlara yada Client ve Server olarak çalışan cihazlara gönderir.

#### Subset Advertisement (Atlküme Bildirim)

VLAN bilgilerini içerir. Açılıp/Kapanması , Alan Adı Oluşturulup/Silinmesi , VLAN isim değişikliği , VLAN paket boyutunun değiştirilmesi gibi bilgileri aynı VTP alan adındaki cihazlara değişikliklerden hemen sonra iletir.

#### Request Advertisement (İstek Bildirim)

VTP alan adı değişikliği, kendi revizyon numarasından yüksek bir revizyon gelmesi, subset mesajı gelmemesi, cihazın açılıp/kapanması gibi durumlarda VTP ile istek bildiriminde bulunur. Request bildirimi alan cihaz önce Summary sonra Subset bildirimi gönderir.

#### GVRP ( Generic VLAN Registration Protocol)

GVRP, network cihazları arasında Vlan bilgilerini aktarmamızı sağlayan IEEE tarafından 802.1Q standartı üzerinde geliştirilmiş bir uygulamadır. VTP ile aynı işi yapar fakat GVRP farklı markalı cihazlardada çalışabilecek şekilde geliştirilmiştir.

GVRP, Vlan bilgilerini taşımak için GARP&#8217;ı kullanır. GARP protokolü bir takım özellikleri GARP PDU içrisinde duyurulması temelli bir uygulamadır. GARP başka protokoller tarafından kullanılarak yapılması gereken işlevleri gerçekleştirir. GVRP&#8217;de GARP PDU&#8217;larının içerisinde Vlan bilgileri taşınır. Bu sayede Vlan bilgileri paylaşılır. 

GVRP, 802.1Q Trunk Portlar üzerinde çalışır ve Gvrp çalıştıran bu portlara GVRP katılımcıları denir. Katılımcı portlara otomatik olarak Vlan atanır veyaz kaldırılır.

Vlan atama ve kaldırma işlemlerine Vlan Registiration ve Vlan Deregistiration denir. Bir Vlan Attiribute Decleration aldığında burdaki Vlan bilgilerini o porta atanır veya Vlan Withdrawall Decleration aldığında burdaki Vlan bilgileri o porttan kaldırılır.

GVRP üzerinde çalışan 3 mesaj türü vardr.

#### Join Mesajları

Join Mesajları sayesinde Vlan Attiribute Decleration yapılır ve bu portlara Vlan bilgileri aktarılır

#### Leave Mesajları

Leave Mesajları, Vlan Withdrawall Decleration işlemini gerçekleştirir ve portlardaki Vlan bilgilerini kaldırır.

#### Leaveall Mesajları

GVRP katılımcılarının tüm Vlan bilgilerini kaldırır.

#### Zamanlayıcılar

Gvrp yukarıda bahsettiğimiz mesaj işlemleri için bazı zamanlayıcı (timer) kullanır.

#### Join Timer

Join mesajı gönderme aralığı belirler. Join mesajlarının iletiminin güvenilirliği için kullanılır. Join mesajı gönderildikten sonra bir timer başlatır. Bu süre bitene kadar karşı taraftan JoinIn (Join Information- Join mesajının alındığına dair geri bildirim) alınmaz ise join mesajı yeniden yollanır.

#### Hold Timer

Bir GVRP katılımcısı yeni bir değişiklik konfigüre ettiğinde veya bir istekte bulunduğunda hemen geri bilidirimde bulunmaz. Hold Timer süresince bekler ve yeni paketler gelirse hepsine tek seferde cevap verir. Böylece bir çok mesaj tek pakette cevaplanır ve gereksiz paket trafiğini engeller.

#### Leave Timer 

GVRP katılımcısı Leave mesajı alındığında hemen Vlan bilgilerini silmez. Yeni bir Join mesajı gelmesini Leave Timer süresince bekler. Eğer Join mesajı gelmez ise Vlan bilgilerini porttan kaldırır.

#### Leaveall Timer

Leaveall süresi sonunda Leaveall Mesajı yollar, Vlan bilgilerini siler ve yeniden oluşturur. Böylece Vlan bilgileri sürekli güncel kalır.

#### GVRP Modları 

VTP&#8217;de olduğu gibi GVRP içinde portları yönetmek için modlar mevcut.

#### Normal Mod

Bu modda port üzerinden Vlan atama işlemleri gerçekleştirilir ve bunları başka kullanıcılara aktarır (duyurur).

#### Fixed Mod

Bu modda Vlan atama işlemleri yapılmaz fakar gelen bilgileri alır ve diğer katılımcılara iletir.

#### Forbidden Mod

Bu modda ne atama işlemi nede duyurma işlemi yapamaz.

#### ISL ve 802.1Q Standartları Nedir?

Ağ güvenliği ve ağdaki network trafiğinin kontrolü (bant genişliğini arttırmak vs.) için ağ VLAN&#8217;lara ayrılır. Ağ içerisinde ayrılan vlanları switch ve routerların tanıyabilmesi için Ethernet Frame içerisine **VLAN Tagging** (Vlan Etiketleme) denilen bir işlem yapılır. Vlan paketinin içerisinde bir bölge bu işlem için ayrılır. Paketi alan switch ve router bu Vlan kimliğine bakarak pakete ne yapması gerektiğine karar verir.Hangi paketin hangi Vlana ait olduğunu etiketlemek için 802.1Q veya ISL (İnter Switch Link) kullanılır.

#### ISL

Cisco tarafından geliştirilen ve ağ trafiğini sağlayan bir protokoldür. Cisco tarafından geliştirildiği için **sadece cisco cihazlarda çalışır.** Switchten routera yada switchten switche çalışabilir.

ISO Modelinde Data Link (Layer2) katmanında çalışır ve trafik sırasında gereksiz bağlantıları azaltır.

Bu protokol 90 Byte&#8217;lık bir kapsülleme işlemi yapar. 26 byte header ve kalan 4 Byte alt bilgidir (Trailer FCS). Başlığa ayrılan 26 Byte&#8217;ın 15 Byte&#8217;lık kısmı Vlan tanıtımı olarak adlandırılmıştır. Her frame kaynak ve hedef Vlan numarası etiketlenir.Ayrıca alıcı ve verici switchlerin fiziksel adresleride bu kısımda bulunur. 

ISL Ethernet frame üzerinde herhangi bir değişiklik yapmaz. Eklemesi gereken bilgileri Ethernet Frame&#8217;in başına ve sonuna ekler ve bu yüzden oldukça güvenlidir. <figure class="wp-block-image">

<img src="http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/ısl.gif" alt="" class="wp-image-144" /> </figure> 

ISL tarafından eklenen verilerle birlikte Frame boyutu 1548 Byte olur. Bu kapsülleme standartları için yüksek bir değer. Boyutu büyük olmasına rağmen trafikte herhangi bir gecikmeye neden olmaz. ISL 1000 VLAN&#8217;ı destekler

#### 802.1Q 

IEEE tarafından geliştirilen bir kapsülleme standartıdır. ISL den farkı Vlan bilgilerini Ethernet Paketinin **içine** ekler. 802.1Q standartı çok büyük ağları daha küçük ve yönetilebilir parçalara ayırmak için kullanılır.

#### 802.1Q Avantajları

  * Açık bir standarttır ve farklı markalı cihazlara uyumlu şekilde geliştirilmiştir
  * 4096 Vlan destekler
  * Kapsülleme olmadan 4 Btye&#8217;lık bir tanıtıcı kısmı vardır.
  * Hedef frame ayrılan alan daha küçüktür.<figure class="wp-block-image">

<img src="http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/802.gif" alt="" class="wp-image-145" /> </figure> 

Cisco cihazların olduğu bir ağ topolojisinde uyumluluk, güvenlik ve hız gibi nedenlerden dolayı 802.1Q tercih edilir.

#### 802.1Q Tag Format <figure class="wp-block-image">

<img src="http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/tagging.jpg" alt="" class="wp-image-147" srcset="http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/tagging.jpg 810w, http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/tagging-300x22.jpg 300w, http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/tagging-768x56.jpg 768w" sizes="(max-width: 706px) 89vw, (max-width: 767px) 82vw, 740px" /> </figure> 

Yukarıdaki resim 802.1Q&#8217;nun ethernet frame eklediği bir frame. Frame 16 bitlik bit TPID, 16 bitlik TCI bölümünden oluşur. TCI içerisinde PCP, DEI ve Vlan ID değerleri bulunur.

#### Tag Protocol Identifier ( TPID )

İlk 16 bitlik alana 0x8100 değerini koyar. Ether Type ile aynı konuma denk gelir. Vlan Tag olan paketler ile olmayan paketlerin ayrılması için eklenir.Yani paket okuduğunda Ether Type yerine TPID okunursa bu pakette Vlan Tagging yapıldığı anlaşılır.

#### Tag Control Information ( TCI )

TPID&#8217;den sonra gelen 16 bitlik alanı oluşturur. Aşağıdaki parçalardan oluşur

#### Priority Code Print ( PCP )

3 bitlik öncelik belirten ( Priority Level) parçadır. Default olarak 3(011) değeri verilir. 

#### Drop Eligible Indicator ( DEI )

1 bitlik bir landır. MAC adresinin canonical olup olmadığını belirtir.  
(canonical: Mac adresinin düzmü tersten mi yazıldığının bilgisi)  
0-) canonical 1-)non-Canonical  
Default olarak &#8220;0&#8221; dır. Günümüz sistemlerinde çok önemli olan bir bilgi değildir.

#### VLAN Identifier ( VID )

12 birlik bir alanda Vlan id bilgisini barındıran bir bölümdur.

#### 802.1ad (802.1QinQ) Nedir?

802.1Q&#8217;nun değiştirilmesiyle ortaya çıkmış bir standarttır. .1Q standardından farkı 1 Vlan header eklemek yerine 4 Byte&#8217;lık 2 tane ekler.<figure class="wp-block-image">

<img src="http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/qinq.jpg" alt="" class="wp-image-149" srcset="http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/qinq.jpg 578w, http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/qinq-300x155.jpg 300w" sizes="(max-width: 578px) 100vw, 578px" /> </figure> 

#### Neden 2 tane header&#8217;a ihtıyaç duyuldu

Aynı servis sağlayıcısından hizmet alan şirketler aynı Vlan ID&#8217;lere sahip Vlanları olduğunda paketlerin istenen yerlere ulaşmaması nedeniyle bu standart geliştirilmiştir.

Herkese ayrı Vlan ID ataması mümkün değil. 4096 tane Vlan bu iş için çok yetersiz. QinQ sayesinde servis sağlayıcı adında (SP-Vlan ID &#8211; Service Provider ID) ikinci bir Vlan header kullanarak Vlan paket karışıklığı önlenir.<figure class="wp-block-image">

<img src="http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/qinq-örnek.jpg" alt="" class="wp-image-150" srcset="http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/qinq-örnek.jpg 686w, http://evrenkorkmaz.xyz/wp-content/uploads/2019/07/qinq-örnek-300x160.jpg 300w" sizes="(max-width: 686px) 100vw, 686px" /> </figure> 

Yukarıdaki şekilde A ve B müşterileri aynı servis sağlayıcısından hizmet almaktadır. A müşterisi 1-100 arası Vlana sahip. B müşterisi ise 1-200 arası Vlanlara sahip. İki müşteride 10 numaralı Vlanlarından istekler yolluyor. Servis sağlayıcı A müşterisi için Vlan 50, B müşterisi için Vlan 100&#8217;ü kullanmaktadır. Bu sayede iki müşteriden de gelen Vlan 10 paketleri karışmadan iletişimini kurabilir. Servis kullanıcısının tanımladığı Vlanı müşteri göremez. Kendi tagging işlemini tamamlayıp yollaması yeterlidir.
