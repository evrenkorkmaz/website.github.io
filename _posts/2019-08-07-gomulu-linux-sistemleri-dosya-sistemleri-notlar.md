---
title: 'Gömülü Linux Sistemleri - Dosya Sistemleri - Notlar'
date: 2019-08-07T14:26:34+00:00
layout: post
categories:
---


#'Gömülü Linux Sistemleri -  Dosya Sistemleri  'Notlar'


**Gömülü Linux**, Linux işletim sisteminin cep telefonları, PDA(Cep bilgisayarları), elde taşınablir medya oynatıcılar ve diğer tüketici elektroniği cihazları gibi gömülü sistemlerde kullanılır.

Gömülü Sistemlerde kullanılan bazı dosya sistemleri;  
1. vfat  
2. ext2 &#8211; ext3 ext4  
3. tmpfs  
4. ramdisk  
5. initramfs  
6. cpio &#8230;

Dosya sistemleri, dosya ve dizinlerin saklandığı yapılardır. Dosya sistemleri ve parametreleri amaca uygun seçilmelidir. Kötü seçimler kaynak istafına ve fiziksel yapının bozulmasına neden olabilir. Linux pek çok dosya sistemine destek verir. Fakat çok az dosya sistemi gömülü sistemler için uygundur. Dosya sistemleri öncelikle cihaza uygun seçilmelidir ve prensip olarak swap kullanılmamalıdır.

Seçilen dosya sistemi için çekirdeğe uygun destek verilmelidir. Dosya sistemleri genelde çekirdekte çok yer kaplar.Kullanılmayan dosya sistemleri çekirdeğe eklenememelidir. Kök dosya sistemi hariç bütün dosya sistemleri modül olarak derlenebilir.

#### VFAT

  * MS-Windows dosya sistemidir. 
  * Linux ihtiyaçlarını karşılamaz. Sahiplik, mod vs işler mevcut değildir.
  * İç yapısı aşırı basit olduğu için daha çok u-boot gibi açılış-yükleyicileri tarafından kullanılır.

#### ext2

  * Linux&#8217;un en eski dosya sistemlerinden biridir.
  * Çok uzun zamandır hakkında bir bug-repot yapılmamıştır.
  * Kök dosya sistemi için gerekli tüm ihtiyaçları karşılar.
  * Ani kapanmalara karşı dayanıksızdır.
  * MMC(Multimedia Card) cihazlarda RO(read only) modu ile bağlanarak kullanılmalıdır. Kök dosya sistemi olarak RW(Read-Write) modunda bağlanılmamalıdır.

#### ext3

  * ext2&#8217;nin devamı ve log tabanlıdır.
  * Ani kapanmalara karşı dayanıklıdır. Dosya bütünlüğü zor bozulur.
  * Yerini ext4 almıştır. Masaüstü sistemlerde artık ext4 kullanılmaktadır.
  * Log tabanlı olduğu için diske yazılmadan önce log tablosunda tutulur sonra diske yazılır.
  * Performans ve dosya büyünlüğü arasında ters ilişki vardır.
  * Crash durumundan sonra e2fsck çalıştırmaya gerek yoktur. Loglar sayesinde dosya bütünlüğünü her zaman sağlar.

#### ext4

  * ext3&#8217;ün devamıdır. ama ext3 ile uyumlu değildir.
  * ext4 dosya sistemi ext3&#8217;ü mount edebilir ve ext3&#8217;e göre daha iyi performans gösterir.
  * Doğrudan ext4 kullanılması tavsiye edilir.
  * Gömülü sistemlerde, MMC tarafında kök dosya sistemi olarak RW modunda mount edilir.

#### tmpfs

  * Sanal belleklte kurulan bir sistemdir.Doldukça büyür, içi boşaldıkça küçülür.
  * virtual memory = ram + swap
  * Ayrılan kapasiteyi RAM&#8217;den çalmazlar. Swap edilebilirler.
  * umount edildiği an veriler kaybolur.
  * Güç kesildiği an veriler kaybolur.

#### RAMdisk

  * Ram&#8217;in disk bölümü gibi kullanılmasıdır(virtual memory değil).
  * Dosya sistemi değildir. Dosya sistemleri için altyapı hazırlar.
  * Sanki bir disk bölümü gibi üzerinde dosya sistemleri kurulabilir.
  * Tmpfs gibi umount edildiğinde veriler silinmez.
  * Cihaz sistemleri /dev/ram0 &#8212; /dev/ram1 gibi olur.
  * Ramdisk çok eski bir teknoloji olarak kalmıştır. Bu yüzden ramdisk yerine tmpfs kullanımı tercih edilir.
  * Ramfs fiziksel bir ortamdır. Bu yüzden ortamı kullanabilmek için üzerinde bir dosya sistemi kurulmalıdır. ext2, ext3, vfat gibi.

#### İnitramfs-I

  * Inıtramfs çekirdeğe gömülü olabilir.
  * Çekirdek tmpfs destekli bir dosya sistemini kök dosya sistemi olarak bağlayabilir. Bu dosya sistemine initramfs denir. Temeli tmpfs dosya sistemidir.
  * Temeli ramdisk olan dosya sistemine ise initrd denir ve artık kulanılmamaktadır. initramfs sistemi çekirdek yüklendikten hemen sonra mount edilir. Genelde modül yüklemek içindir veya asas kök dosya sistemini bağlamadan önce ön hazırlıkları yapmak için kullanılır.
  * Çekirdek önce kendini açar sonra da cpio arşivini bit tmpfs dosya sistemi içine açar. Bu dosya sistemine initramfs denir ve çekirdek bu dosya sistemini kök dosya sistemi olrak bağlar.

#### Initramfs-II

  * Initramfs, çekirdekten ayrı kurulabilir. Çekirdek bilinen yolla derlenir.
  * uImage ve uramfs beraber kullanılır. uramfs içindeki programların GPL sınıfından olması gerekmez. Güncellemelerde sadece uramfs kullanılır, çekirdek derlenmez.
  * u-boot, önce çekirdeği sonra uramfs&#8217;i yükler ve çekirdeüe uramfs&#8217;in yüklendiği adresi ve boyutunu parametre olarak geçirir.

#### Cpio

  * Cpio bir dosya sistemi değildir. Bir arşiv programıdır. En kaba tabir ile bütün dosyaları alt alta ekler.
  * Tar programı ile aynı sınıftafır.
  * İnitramfs sistemleri için de arşivleme yapar.
  * Cpio kodu çekirdek kodu içinde gömülü durumdadır.
  * Aynı anda input ya da output modunda çalışır.
  * Output modu paketleme , arşivleme yapar.

#### Nfs(Network File system)

  * Nfs, ağ üzerinden dosyalara erişim tekniğidir. Yani ağ üzerinden dizin paylaşım yönetimidir.
  * Kök dosya sistemine ağ üzerinden bağlanılabilir ya da host üzerindeki herhangi bir dizin export edilebilir.
  * no\_root\_squash sayesindee root yetkisi ile işlem yapılabilir.

#### Cramfs

  * Sıkıştırılmış dosya sistemidir.
  * Mount edildikten sonra da sıkıştırılmış olarak kalır, açıklarak kullanılmaz.
  * Dosya boyutu 16mb’yı geçemez.
  * FS büyüklüğü 256mb tan büyük olamaz.
  * Çok az yer kaplar. Gömülü sistemler için uygundur.
  * Dosya boyutu 16mb’yı geçemez.

#### Romfs

  * Çok az yer kaplayan , sadece ro erişim yapabilen bir dosya sistemidir.
  * Doğrudan donanım içine gömülen dosya sistemleri için uygundur.

#### Squashfs

  * gzip- lzo – xz ile sıkıştırılmış salt okunur dosya sistemidir.
  * Cramfs&#8217;e göre daha iyi sıkıştırma yapar.
  * uid/gid timestamp desteği vardır.

#### Zram

  * Masaüstü sistemlerde swapın hızı ve kapasitesini arttırmak için kullanılır.
  * Her zaman modül olarak derlenmesi tavsiye edilmektedir. Böylece disk sayısı amaca göre atanabilir.
  * /var , /tmp gibi dizinler içinde kullanılabillir. Fatak tmpfs gibi olmayıp doğrudan ram&#8217;den kapasite çalınır.

#### eCryptfs

  * Şifreli bir dosya sistemidir.
  * VFS layer üzerinde işler
  * Stack based desteklidir. Bundan dolayı mevcut herhangi bir dosya sistemi üzerinde şifreleme yapabilir.
  * Önce boş bir dizine mount edlir. Sonra bu dizine kopyalanır. Diske yazılan dosyalar artık şifrelenmilştir.
  * İçi dolu dizinler mount edilmemelidir.

#### Ubifs

  * Flash diskler için geliştirilmiş ,kapanmaya dayanıklı ve ubi üzerinde kurulan, log tabanlı bir dosya sistemidir.
  * Diğer dosya sistemleri gibi doğrudan kurulup mount edilmez, ön hazırlık gerekir.
  * Tamamen boş bir diske mount edilirse otomatik olarak ubifs kurulur.
  * Fsck programı yoktur. Mount sırasında hata olursa dosya sistemi düzeltmeye çalışır.
  * Düzelirse mount eder düzeltmezse ya ro mount eder yada hiç mount etmez.
  * Loop mount desteği yoktur , simulatör ile mount edilir.

#### Pseudo File System

  * Kernel ve user space arasında iki yönlü bilgi alışverişi için tasarlanmıştır.
  * Dosya ve dizinler her zaman ya boot anında ya da tam kullanım anında kurulurlar ve sistem kapanınca yok olurlar. Fiziksel olarak birdiskte kurulmazlar bundan dolayı dosya boyları her zaman 0’dır ama içleri doluolabilir.

#### MS-DOS Bölümlendirmesi

  * Disklerin tek bir blok olarak kullanılması pratik değildir.Diskler, daha ufak disk parçalarına ayrılarak kullanılırlar. Bu ayırma işlemine bölümlendirme denir. Linux pekçok disk bölümlendirmesini destekler.
  * MMC diskler için MSDOS bölümlendirmesi, NAND diskler için mtdparts(NAND Disk Partition Types) bölümlendirilmesi kullanılır.Her iki bölümlendirme desteği çekirdeğe verilmelidir.
  * Gömülü sistemlerde, MMC üzerinde genelde 2 bölüm kullanılır. İlk bölüm u-boot&#8217;un desteklediği bir dosya sistemi olur, vfat/ext2/ext3/ext4 gibi.

#### MTD Bölümlendirmesi

  * MS-DOS bölümlendirmesinde, bölümlendirme tablosu , diskin 1. sektöründe saklanır. Bu sektöre MBR denir. Çekirdek, MBR&#8217;yi okuyarak bölümlendirme hakkında bilgi sahibi olur.
  * Fakat NAND disklerde MBR kavramı yoktur. Çekirdek, mtdparts parametresiyle NAND bölümlendirmesinden haberdar olur. mtdparts değişkeni bir kaç yoldan çekirdeğe verilebilir.

#### /dev/null

  * /dev/null Unix benzeri işletim sistemlerinin aygıtlar dizininde bulunan bir stream dosyasıdır. Bu belgeye yazılan her tür ve herhangi miktarda olan veriyi sistem yok sayar ve yazan işleme EOF(end of file) gönderilir. Bu açıdan /dev/null bir kara deliğe benzetilir

#### /dev/zero

  * İçinde sonsuz adet &#8220;0&#8221; bulunan cihazdır.

#### /dev/random &#8211; /dev/urandom

UNİX türevi işletim sistemlerinde **/dev/random** yalancı rastsal sayılar üreten bir stream dosyasıdır. Cihaz sürücülerinden ve diğer kaynaklardan toplanan çevresel gürültüye erişim sağlar. Bloklama ile çalışır. /dev/random normalde talep edildiğinden daha az entropi mevcutsa engeller, /dev/urandom tipik olarak asla engellemez

#### /dev/full

  * uygulama testlerinde &#8220;tam dolu dosya&#8221; testi için kullanılır.

#### rsync

  * Genelde farklı 2 makine içindeki dizinleri es tutabilmek için yani senkronizasyonu için kullanılır.
  * Özellikle gömülü sistemlere program aktarmak veya güncellemleme yapmak için çok elverişlidir.
  * Taşıma sırasında sıkıştırma yapar. Bütün dosya özelliklerini korur. En önemlisi ssh üzerinden işler, güvenlidir.

#### SSH

  * Hedef dosyanın, bağımlılıklarına göre nasıl elde edileceğini tarif eden dosyaya makefile denir. İki temel kavramı vardır: girişler ve bağımlılıklar.

#### /etc/inittab

  * Çekirdek, kök dosya sistemi bağlandıktan sonra, genelde /sbin/init programı işletir. Bu program sistemi ayağa kaldırır. Sistemi ayağa kalkma şekli /etc/inittab dosyası tarafından tespit edilir.

Kaynak : [ucanlinux.com](http://ucanlinux.com/)
