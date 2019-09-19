---
title: 'Andrew Tanenbaum -Modern Operating Systems - Notlar'
date: 2019-09-12T11:06:45+00:00
layout: post
categories:
---
 
#'Andrew Tanenbaum -Modern Operating Systems - 'Notlar'



 * Modern bilgisayarlar; memory,disk,printer,fare,klavye, network interface gibi giriş çıkış aygıtlarından oluşan karmaşık bir sistemdir. Bu karışık sistemin yönetimi **İşletim Sistemleri (Operating System)** sayesinde düzgün bir şekilde çalışır.

  * Aslında bir ikona tıkladığımızda çalışan işletim sistemi değildir fakat ikonun yazılımı işini tamamlamak için işletim sistemini kullanır.
  
[![os.png](https://i.postimg.cc/SRvMqFrZ/os.png)](https://postimg.cc/0K7Qd4hm)

  * Yukarıda basit olarak bilgisayar bileşenlerinin olduğu bir tablo mevcut. En alt katman yani Hardware; çipler,boardlar,fare/klavye,monitör gibi fiziksel aygıtlar mevcut.

  * Çoğu bilgisayarda 2 çeşit operasyon modu vardır: kernel mod(supervisor mod) ve user mod. Kernel mod bütün donanıma erişimi olan ve her şeyi çalıştırma yeteneğine sahip kullanıcı modudur.

  * İşletim sistemi ile normal bir yazılımın ayrılmasının en büyük sebebi; eğer kullanıcı kullandığı bir e-mail programını sevmez ise bunu değiştirmekte veya kendisi yeni bir program yazmakta özgürdür fakat sistemin işleyişi ile ilgili değişiklikler yapmakta özgür değildir. Bu gibi sebeplerden dolayı çoğu işletim sisteminde sistemin bütünlüğü ve güvenliği için programlar kullanıcı modunda çalışır.

#### İşletim Sistemi Nedir

  * İşletim sistemi çekidekte çalışan yazılımlar olduğu söylenebilir fakat bu herzaman doğru değildir. Temel olarak birden fazla alakasız işlemi yerine getirmesidir.

  * İşletim sistemi; uygulama programcılarına dağınık bir donanım yerinde temiz,soyut bir kaynak kümesi sağlamak ve bu donanım kaynaklarını yönetmektir.

  * Çoğu zaman donanım tasarımcıları yazılım için ne kadar zorluk çıkardıklarının farkında değil. İşletim sisteminin en önemli görevlerinden biri donanımı gizlemek ve çalışmak için temiz,zarif ve tutarlı soyutlamalar yapmaktır.

#### Bilgisayar Donanımı

**Processors (İşlemciler)**

  * Bilgisayarın beyni CPU&#8217;dur. Bilgileri çalıştırmak için hafızadan veri çekip geri yollar.
  * Çoğu CPU kernel ve user modu kullanır. Kernel moddayken CPU her şeyi çalıştırabilir.,
  * Kullanıcılar ise her zaman user modda çalışır. Bu yüzden kernel üzerinde işlem yapmak yetkileri yoktur. Bu yüzden user modda sistem çağrıları (System Call) kullanılır. User modda kernelde bir işlem yapılcaksa (dosya kopyalama vs.) önce arka planda **Trap** isteği(prodesürü) döner, sonraısnda arka planda kernel moda geçip işini halledip tekrardan user moda geçer. 

**Memory (Bellek)**

[![os1.png](https://i.postimg.cc/k5JXjYND/os1.png)](https://postimg.cc/sMLrxcNR)

  * Bilgisayar için en önemli 2. bileşen memory&#8217;dir.
  * Memory hızlı olduğu kadar boyutu büyük ve ucuzda olmalıdır. Yukarıdaki resme dayanarak gelen verileri boyutlarına göre nasıl gruplayıp yöneteceğini bilmesi lazım.
  * Donanım tarafından kontrol edilen **cache memory** var. Main memory cache line için 64 Byte&#8217;lık parçalara böler. İlk line (Line 0) 0-63. adresler, 1. line 64-127. adresler olarak ilerler. Çok sık kullanılan işlemlerin hızlı bir şekilde halledilmesi için kullanılır. Program Memoryden bir kelime okumak istedğinde önce cache içine bakılır. Cache içindeyse tekrardan bu bilgiyi memoryden çekmesine gerek yoktur direk cache içindeki bilgiler kullanılır.
  * Cache&#8217;in bir diğer kullanım alanı işletim sistemi bir program çalıştırırken başka bir programı çalıştırmak istedğinde ilk program kapatılır ve diğer program çalıştırılır. Bu kapatma işleminde program yeniden çalıştırıldığında baştan başlamaması için ilk program cache&#8217;te tutulur ve tekrardan kullanılacağında program cacheten kaldığı yerden çalışmaya devam eder.
  * Cache genel olarak tekrar tekrar aramaları önlemek için performans amaçlı yapılmış bir sistemdir (Web sitelerinin ip adreslerini daha sonra kullanmak için saklaması gibi). Ama zamanla cache sisteminde bazı sorunlar ortaya çıktı:
  * &#8211; Yeni bir line hangi satıra gelecek.
  * &#8211; Yeni bir slot gerektiğinde hangi slot silinecek

  * **EEPROM( Elektrically Erasable PROM)** veya flash memory. Romdaki gibi geçici değil kalıcı olarak verileri saklar. Veriler silinip yeniden yazılabilir ve hem okuma hemde yazma yapar.
  * Flash memory küçük taşınabilir elektronik aletlerde sıkça kullanılır. Bunlar müzik çalar, kameralar vb. cihazlardır. Çok fazla silme işlemi yapılırsa zamanla yıpranır.

**Disk**

[![disk.png](https://i.postimg.cc/V63JHBLT/disk.png)](https://postimg.cc/mzyZP99N)


* Hiyerarşinin son kısmı disklerdir. Diskler Ramlere göre 2 kat daha ucuz ve 2 kat daha büyüktür. Tek sıkıntı disk üzerindeki verilerin erişiminin yavaş olması ve bununda nedeni mekanik bir çihaz olmasıdır.
  * Diskler 5400,7200,10800 rpm ve daha hızlı dönebilen metal plakalardan olurur.Bilgiler disk üzerindeki eş merkezli halkalar üzerine yazılır. Okuyucu kollar bu halkaların üzerindeki izleri(sektörleri) okuyarak bilgiye erişir.

**I/O Devices (Giriş/Çıkış Aygıtları)**

  * İşletim sisteminin yönetmesi gereken donanımlar CPU ve memory ile kısıtlı kalmıyor. I/O aygıtları işletim sistemi ile çokca etkileşime geçen cihazlardır. I/O aygıtları genel olarak 2 kısımdan oluşur. 1- Kontrol cihazı 2- kendisi.
  * Bu cihazların fiziksel olarak kontrolü için çip yada bir çip seti bulunur. Cihazdan gelen veriyi okur ve işlemi yerine getirir. Bu cihazların kontrolü göründüğü kadar basit değildir.Aslında karmaşık bir sistemdir. Bu cihazların işletim sistemi ile anlaşabilmesi için arayüzleri vardır ve bu arayüzler bu cihazlar tarafından sağlanır. Örneğin bir disk yöneticisi, 2. diskten 1100. sektörü okuması için bir komut alabilir. Bu komutu aldıktan sonra disk yöneticisi okuma yaptığı kolları bu sektörlere yönlendirerek verilen sektörleri okur ve işletim sistemi ile paylaşır. Bu okuma işlemi işteim sisteminin değil disk yöneticisinin işidir. 

**Buses**

  * İşlemciler ve momorylerin hızlanmasından dolayı tek bir bus hattı bu hız için yetersiz kaldı. Bu yüzden bu duruma ayak uydurmak için bilgisayar mimarisine bus hatlatı eklendi.
  * Sistemde bir sürü bus hattı bulunmakta(PCI,PCIe,USB,SATA). Her birinin farklı transfer oranı bulunmakta. Ana bus hattı PCIe(Peripheral Component Interconnetc express) hattıdır.
  * PCIe Intel tarafından geliştirilmiş PCI buslarının gelişmiş halidir. Bu kablolar saniyede 10 Gigabit transfer yapabiliyor. Önceki bus hatlarının oranlarının çok daha hızlı.
  * Aynı bus hattı üzerinden birden fazla cihaz iletim sağlar. Yani bus hattını tek seferde sadece 1 cihaz kullanabiliyor. Bu yüzden bus hattı kullanılacağında kimin kullanacağına karar veren bir mekanizmaya ihtyiyaç duyuldu. Bu bus mimarisine **Shared Bus Architecture (Paylaşılmış Bus Mimarisi)** denir.Fakat PCIe özel olarak noktadan-noktaya (point-to-point) bağlama kullanılır.
  * **PCI&#8217;da Parallel Bus Architecture ( Paralel Bus Mimarisi )** kulanılır. Bu mimariye göre veriler birden fazla kablo üzerinden iletimini sağlaması anlamına gelir. Yani 32 Kablo üzerinden 32 bitlik olan veriler, her kablodan 1 bit yollanarak paralel olarak iletilir. Fakat PCIe Serial Bus Arch. kullanır ve 32 bitlik veriyi tek kablo üzerinden tek seferde paket halinde yollar(Network paketleri gibi). Basit bir yapıdır çünkü 32 bitinde aynı anda ulaştığını kontrol etmek gerekmez. Paralel bus mimarisi hala kullanılıyor. Bu iki mimari birleştirilerek 32 bitlik paketler 32 kablo ile tek seferde 32 mesaj iletebiliyor.

**Booting The Computer**

  * Her bilgisayarın bir anakartı vardır. Her anakartın BIOS ( Basic Input Output System) denilen bir programı vardır. BIOS ; başlıca low-level I/O yazılımı, klavyeyi okuma prosedürleri, ekrana yazma ve disk I/O arasında çalışır. BIOS bu bilgileri günümüzde kalıcı olan fakat BIOS tarafından bulunan hataları fixleyip (Hataları fixleyen BIOS değil) update edebilecek Flash RAM&#8217;lerde tutuluyor.
  * Bilgisayar başlatıldığında BIOS çalışmaya başlar ve ilk olarak ne kadar RAM olduğunu konrtol eder ve giriş cıkış aygıtlarını kontrol eder. PCI ve PCIe bus hatlarından tarama yaparak bu cihazların bağlı oldup olmadıklarını kontrol eder. Eğer cihazlar farklı konumlarda ise yeniden yapılandırarak boot edilir.

#### İşletim Sistemi Türleri

**MainFrame OS**

[![mainframe.jpg](https://i.postimg.cc/BQNqjBf0/mainframe.jpg)](https://postimg.cc/fJt4FXb2)

  * Oda büyüklüğündeki data center&#8217;lar için kullanılır. Bu bilgisayarlar kişisel bilgisayarlardan farklı giriş çıkış kapasitesine sahiptir. 1000 Disk ve milyonlarda Gigabit data bu cihazlar için normal değerlerdir. 
  * Mainframeler, üst düzet web sunucuları,büyük ölcekli ticaret web siteleri için sunucular veya işletmeler arası iletişim için kullanılır.
  * Mainframelerin İşletim sistemleri bir sürü giriş-çıkış işlemini aynı anda yapabilecek şekilde yapılmıştır.

**Server Os**

  * Mainframelerin bir alt seviyesidir. Çok büyük kişisel bilgisayarlar, iş istasyonları hatta mainframe olan sunucularda çalışırlar. Birden fazla kullanıcıya aynı anda hizmet ederler ve kullanıcıların yazılım ve donanım paylaşımına izin verir. Serverlar dosya servisi,yazma servisi , ve web servisi sağlayabilir.

**Multiprocessor OS**

  * Birden fazla CPU&#8217;nun bir işletim sistemine bağlanmasıdır. Tam olarak nasıl bağlandıklarına ve ne paylaştıklarına göre &#8220;paralel computer&#8221; , &#8220;multi computer&#8221; veya &#8220;multiprocessor&#8221; denir.
  * Özel işletim sistemlerine ihtiyaçları vardır. Ancak bunlar genellikle iletişim, bağlantı ve tutarlılık için özelleştrilmiş işletim sistemleridir.

**Personal Computer OS** 

  * Modern bilgisayarların hepsi multiprograming yapabilir ve açılışlarında bir sürür program çalıştırırlar. Amaçları tek bir kullanıcıya iyi bir destek sağlamak. Genellikle oyun ve internet erişimi için kullanılır.

**Handheld Computer OS**

  * Tablet ve telefon gibi PDA( Personal Digital Assistans) denilen el bilgisayarlarıdır.

**Embedded OS**

  * Gömülü sitemler bilgisayar olarak görülmeyen ve kullanıcılar tarafından yüklenen yazılımları kabul etmeyen, aygıtları kontrol eden bilgisayarlardır.
  * Örnek olarak; arabalar,mikrodalga fırınlar, Tv setleri, mp3 çalar vb.
  * Kısacası el bilgisayarlarından ayıran en büyük özellik güvenilmeyen yazılımları kabul etmemesidir.Mikrodalga fırına yeni bir app yada yazılım yükleyemezsiniz. Kendi yazılımının hepsi ROM&#8217;dadır.

**Sensor-Node OS**

  * Birçok amaç için kullanılabilir. Bir baz istasyonuyla kablosuz iletişim kurabilen küçük bilgisayarlardır. Örnek olarak; bina çevre güvenliği, ulusal sınırların güvenliği, yangın alarmı, orman yangını alarmı, hava durumu, savaş alanındaki düşmenın hareketleri gibi bir çok alanda kullanılabilir.

**Real-Time OS**

  * Bu sistemler anahtar bir parametre olarak zamana sahip olmalarıyla karakterize edilir. Örneğin; bir fabrikadaki üretim hattı ile ilgili gerçek zamanlı veri toplama vs. işlemler için kullanılabilir.

**SmartCard OS**

  * Bir işlemci yongası içeren kredi kartı büyüklüğünde cihazlar olan akıllı kartlarda çalışır. Bazıları sadece ödeme işlemleri için kullanılır fakat daha fazlasınıda yapabilirler.
  * Bazıları okuyucunun içerisinde yerleştirdiği temaslar tarafından desteklenir fakat temazsız kartlar yapabileceklerini büyük öl.üde kısıtlar.

#### **İşletim Sistemi Konsepti**

**Processes**

  * İşetim sistemleri için anahtar konsept işlemlerdir(Process). Bu işlemler basit olarak yürütülmekte olan bir programdır. Bağlantılı her işlem için **address space** oluşturur ve memory adresi 0 dan başlayarak okuma yapar. Prosesler okuma ve yazma işlemi yapabilir. Adress space&#8217;leri çalıştırlabilir programları, program datası ve veri yığınları içerir.Aynı zamanda her işlemle ilgili olarak genellikle kayıtlar ( program counter ve stack pointer), açık dosyaların listesi, olağanüstü alarmlar, işlemlerin listeleri ve programı çalıştırmak için gerekli bilgiler dahil olmak üzere bir dizi kaynak bulunur.Bu süreç genel olarak programı çalıştırmak için gerekli verilerin olduğu bir konteyner diyebiliriz.

**Address Spaces**

  * Her bilgisayar çalışan prosesleri hafızada(main memory) tutmak için bazı alanlara ihtiyaç duyar. Basit işletim sistemlerinde sadece tek bir program çalışabilir. İkinçi bir program çalışacapında ilkini kaldırır ve ikinçi programı çalıştırır.
  * Daha karmaşık işletim sistemlerinde memory birden fazla programı aynı anda çalıştırabilir. Birbirlerine ve işletim sistemine müdahale etmelerini engellemek için bir koruma mekanizmasına ihtiyaç vardır. Bu mekanizma donanım içerisinde olmak zorunda fakat işletim sistemi tarafından kontrol edilir.
  * Yukarıdaki bakış açısı bilgisayarın ana belleğini yönetmek ve korumakla ilgilidir. Farklı ama aynı derecede bir sorun, bu işlemlerin addres space&#8217;lerini yönetmektir.

**Files**

  * Bütün işletim sistemleri tarafından desteklenen bir anahtar kavram dosya sistemleridir. Dosya sistemlerinin temel işlevi, disk ve I/O aygıtlarının özelliklerini gizlemek ve programlayıcıya cihazlardan bağımsız güzel ve temiz bir soyut ortam oluşturmaktır. Sistem çağrıları; dosya oluştrumak,silmek,okumaj ve dosyalara yazmak için geçerlidir. Dosya okumak istediğinde dosya bulunmalı, açılmalı, okuduktan sonra tekrar kapanmalıdır. Bu sayede bu işlemleri yapmak için sistem çağrıları tekrarlanır.

**Input/Output**

  * Her bilgisayar girdi ve çıktı üretmek için fiziksel olarak I/O cihazları kullanır. Bir çok çeşidi vardır. Örnek olarak; klavye/fare, monitör, printer vb. Bu cihazları işletim sistemleri yönetir.

**Protection**

  * Bilgisayar kullanıcılarının genellikle korunmak ve gizli tutmak istedikleri bilgiler içerir. Sistem güvenliğini yönetmek, örneğin dosyalara sadece yetkisi olan kullanıcılar tarafından erişilebilecek şekilde işletim sistemine bağlıdır
  * Unix dosyalarında bu işlem için 9 bitlik binary koruma kodu vardır. Bu koruma bitleri üçerli gruplar halinde değerlendirilir. İlk üçlü dosya sahibi, sonraki üçlü grup kullanıcıları ve son üçlüde diğer kullanıcılardır. Bu üçlü gruplarda kendi içlerinde okuma,yazma ve çalıştırma yetkisi olarak ayrılır(rwx). Dosyalar içinde çalıştırma yetkisi (x) arama yapma iznine denk gelir. Tek çigzi ise o kullanıcıların izninin olmadığı anlamına gelir.

**The Shell**

  * İşletim sistemine sistem çağrıları yapan koddur. Editörler, derleyiciler, yardımcı programlar vs. önemli ve faydalı olsalar bile işletim sisteminin bir parçası değildir.
  * Shell; kullanıcı GUI kullanmıyorsa terminal kullanan kullanıcı ile işletim sistemi arasındaki arayüzdür. Bir çok shell çeşidi vardır; sh,csh,ksh,bash ve fish bunlara örnek olarak gösterilebilir. Hepsi orjinal kabuk olan Shell(sh)&#8217;dan türemiş ve aynı işlevleri, destekleri sağlar.
  * Kullanıcı sisteme login olduğunda sehll çalışmaya başlar. Shell standart girdi ve çıktılara sahip olarak terminal üzerinde çalışır. Kullanıcıya kabuğun bir komutu kabul etmeye hazır olduğunu söyleyen dolar($) işaretini koyarak bekler. Kullanıcı örnek olarak bir &#8220;**date**&#8221; komutu girince shell bir **child process** oluşturur ve date programını child olarak çalıştırır. Eğer child olarak çalışıyorsa sonlanmasını bekler. Child process bittiğinde $ işareti koyarak yeni bir komut bekler. 

#### System Call (Sistem Çağrıları)

  * İşletim sisteminin 2 ana görevi vardır. Bunlar kullanıcılara soyutlanmış bir çalışma ortamı sağlamak ve bilgisayarın kaynaklarını yönetmektir. Örneğin; bir kullanıcı bir dosya oluşturup, yazıp, silme işlemi yaptığında bu işlem kullanıcı programları ve işlemtim sistemleri arasındaki etkileşimle sağlanır. Kaynak yönetimi kısmı bu işlemleri kullanıcıya göstermeden(soyutlayarak) ve otomatik olarak halleder. Bu nedenle kullanıcı programları ve işletim sistemi arasındaki arayüz öncelikle soyutlamalar ile ilgilenir. İşletim sisteminin neler yaptığını anlamak için bu arayüzleri inceleyebiliriz. Arabirimlerde temel konsept benzer olmasına rağmen işletim sistemleri arasında farklılık gösterebilir.
  * Bir kullanıcı programı çalışıyorsa dosyadan veri okuma gibi işlemler için sistem çağrılarına ihtiyacı vardır. İşlemi, işletim sistemine aktarmak için &#8220;**trap**&#8221; komutunu çalıştırmak zorundadır. Ardından işletim sistemleri parametleri inceleyerek çağrı süresinin ne istedğini belirler. Sistem çağrısını gerçekleştrir ve sistem çağrısını izleyen talimatlara göre geri döndürür. Sadece sistem çağrıları çekirdeğe girer. 

#### Operating System Structure

**Monolithic System**

  * Sistemde çalışan işlemleri tek bir işlemmiş gibi çalıştırır. Biri çökerse hepsi çöker.

**Layered System**

  * Bu sistem mimarisi Monolithic&#8217;den farklı olarak hiyerarşik şekilde sınıflandırılmış katmansal bir yapı içerir.
  
[![layered.png](https://i.postimg.cc/157kxCZf/layered.png)](https://postimg.cc/YhgnYx0H)

  * Bu sistemin 6 katmanı mevcut. İlk yanı 0. katman kesicilerin meydana geldiği yada zamanlayıcıların süresi dolduğu zamankisüreçleride geçiş yapan işlemcinin tahsis edilmesiyle ilgilidir. Kısacası 0. katman CPU&#8217;ya çoklu işlem sağlamaktadır.
  * 1. Katman; hafıza yönetimi için. Main memorydebulunmayan işlemler için Main memory&#8217;de yer ayırma işlemini yapar.
  * 2. Katman, işlem ile operatör(kullanıcı) arasındaki iletişimi sağlar.
  * 3. Katman, I/O cihazlarını yönetmek ve gelen verilerin iletimi ile ilgilenir.
  * 4. Katman, kullanıcı programlarının olduğu yerdir. Proses, memory consol ve I/O ile ilgilenmez.
  * 5. Katman ise sistem operatörü işlemi içindir.

**Microkernels**

  * Layered yaklaşımında kernel ve user arasındaki sınırı çizebilecekleri bir yaklaşım vardır. Geleneksel olarak bütün katmanlar kernele yollanır fakat bu gerekli değildir. Kerneli daha az kullanmak daha faydalı olacaktır çünkü herhangi bir bug kernel sistemini de düşürebilir. Yani daha az kullanarak Fatal Errorlardanda kaçınabliriz.

**Client-Server Model**

  * Microkernel fikri daha sonralarda işlemleri yapacak 2 sınıfa ayrıldı. İlki server; servisleri sağlayan. 2.si ise client; bu servisleri kullanan.
  * Client ve server iletişim mantığı birbirlerine mesaj yollamaktır. Client almak istediği servisi/bilgiyi mesaj atar ve server bu bilgiyi iletir.
  * Client ve Server aynı makinede çalışabilir fakat bazı optimizasyonlar yapılmalıdır. Genel olarak client-server modeli 2 farklı cihaz arasında local ağ üzerinde haberleşmesi üzerinde yapılmış bir modeldir.

#### Virtual Machines 

**Vm/370**

  * IBM tarafından geliştirilmiş bir işletim sistemidir. Birden fazla kullanıcıya erişim izni veren ve hepsine farklı işletim sistemleri sağlayabilen bir sistemdir. Saf donanım üzerine(bare hardware) kurularak kullanılır. Her sanal makine doğrudan donanımı kullanabilir.
  
  [![vm.png](https://i.postimg.cc/X7B8JQCZ/vm.png)](https://postimg.cc/BXsD75Ns)

Vm/370 üzerinde sanal olarak birden fazla CMS(Conversational Monitor System) kurularak kullanıcılara birden fazla sanal işletim sistemi sağlar. CMS programcılar tarafından popüler ve çoğunla tercih edilem bir sanal makinedir. 

**Virtual Machine Rediscovered**

  * **Time sharing** mantığı değilde **Shared Host** modeli yapılmaya başlanmış. Günümüzdeki modern Virtual Machine sistemi ortaya çıkmıştır. 

**Exokernels**

  * Gerçek makineyi clonlamak yerine, onu alt bölümlere ayırmak, başka bir deyişle her kullanıcıya kaynakların alt kümelerini vermektir.
