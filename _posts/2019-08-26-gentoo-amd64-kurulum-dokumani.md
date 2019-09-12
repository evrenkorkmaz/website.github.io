---
title: Gentoo amd64 Kurulum Dökümanı
date: 2019-08-26T12:11:23+00:00
layout: post
categories:
---

# Gentoo amd64 Kurulum Dökümanı

#### **Gentoo Nedir?**

Gentoo, kaynak kod temelli bir Linux dağıtımıdır. Kaynak kod tabanlı kurulumunun zorluğu sebebiyle, diğer Linux dağıtımları kadar popüler olamasa da; kaynak kurulumlu dağıtımlar arasında en popüleridir. Özelleşmiş Linux dağıtımları hazırlanırken temel olarak kullanılmaya uygundur. 

#### Kurulum

Gentoo imajını USB bellek veya CD ile boot ettikten sonra karışımıza aşağıdaki ekran geliyor.<figure class="wp-block-image">

<img src="http://evrenkorkmaz.xyz/wp-content/uploads/2019/08/gento-login.png" alt="" class="wp-image-196" srcset="http://evrenkorkmaz.xyz/wp-content/uploads/2019/08/gento-login.png 723w, http://evrenkorkmaz.xyz/wp-content/uploads/2019/08/gento-login-300x157.png 300w" sizes="(max-width: 723px) 100vw, 723px" /> </figure> 

**boot:** olarak gelen satırda kernel ve hardware option ekleme kısmı geliyor.  
Seçebileceğimiz kerneller:  
**gentoo :** Varsayılan kernel.  
**gentoo-nofb :** gentoo ile aynı fakat framebuffer desteği yok.  
**memtest-86 :** Local RAM deki hataları test etmek için.  
Seçilebilecek bir sürü hardware option var. Bunları [gentoo- amd64-Handbook](https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/Media) sitesinden bulabilirsiniz 

Biz kernel olarak varsayılan kernel gentoo ve hardware option kısmına dopcmcia yazıyoruz.  
**dopcmdia :** önyükleme için kullanmamız gereken parametre.

<pre class="wp-block-preformatted">boot: gentoo dopcmcia</pre>

Bu komutu yazdığımızda sistem otomatik olarak önyükleme başlatıyor. Önyükleme sırasında klavye ayarlarını değiştirmek isterseniz 10 saniyelik bir durakasama yapıyor ve sistemde kayıtlı klavyelerin listesini çıkartıyor. 10 saniye içinde seçmezseniz default olarak US-keyboard seçip önyüklemeye devam ediyor. Önyükleme bittikten sonra otomatik olarak gentoo ortamına root olarak giriş yapıyoruz. Terminaller arası geçiş için ALT+F1 ALT+F2 kısayolları ile geçiş yapabilirsiniz. 

Ekstra hardware konfigürasyonu önyükleme sonrasında **root#** kısmından da yapabilirsiniz. Bu konfigürasyonlarıda [Gentoo Handbook &#8211; Choosing right installation](https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/Media) kısmında mevcut.

**Root Parolası Ekleme** 

Kurulum bittikten sonra sisteme root olarak girmek için root parolası tanımlamamız gerekiyor. 

<pre class="wp-block-preformatted">root: passwd</pre>

**SSH servisi (Opsiyonel)**

SSH servisinden yaralanmak için:

<pre class="wp-block-preformatted">root: rs-servive sshd start</pre>

#### Gentoo Network Yapılandırması 

Kullandığınız sistem DHCP sunucusuna bağlı ise network konfigürasyonunu otomatik olarak yapılmıştır.  
Kurulumda gelen ssh,scp,ping,wget ve link komutları mevcut. Konfigürasyonlar tamam ise bu komutlar çalışır.

**Network Interface Bulma**

**ifconfig** komutu ile network arayüzlerine bakabilirsiniz. Ip adresi (inet) varsa ping atarak kontrol ediniz. 

<pre class="wp-block-preformatted">root: ping 8.8.8.8</pre>

**Otomatik olarak Network Kofigürasyonu** için **&#8220;net-setup&#8221;** komutunu kullanabiliriz. **net-setup** komutunum yanına güncellemek istedğimiz arayüzü ekleyerek komutu çalıştırıyoruz.Bu komut sayesinde network arayüzünü güncelleyip otomatik ip adresini alabliriz. 

<pre class="wp-block-preformatted">root: net-setup eth0</pre>

Buraya kadar eğer otomatik ip adresi alamadıysak bu işlemi manuel olarak yapmamız gerekiyor. Manuel olarak network konfigürasyonu için [Gentoo-Handbook](https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/Networking)tan yardım alabilirsiniz.

#### Disk Yapılandırması 

Linux sistemlerde disk blokları küçük ve yönetilebilir parçalara ayrılır. Diğer işletim sistemlerinde isterseniz bunu otomatik olarak yapar. Disk bölümlendirmede 2 ana standart disk bölümlendirme teknolojisi vardır: MBR ve GPT. Biz bu kurulumda GPT kullanacağız. Diski bölümlendirirken önerilen boyutlar aşağıdaki tabloda mevcut. Bu önerilen boyutlarda disk bölümlendirmesi yapacağız.

<table class="wp-block-table">
  <tr>
    <td>
      <strong>Disk Bölümü</strong>
    </td>
    
    <td>
      <strong>Dosya sistemi</strong>
    </td>
    
    <td>
      <strong>Boyut</strong>
    </td>
    
    <td>
      <strong>Açıklama</strong>
    </td>
  </tr>
  
  <tr>
    <td>
      /dev/sda1
    </td>
    
    <td>
      (bootloader)
    </td>
    
    <td>
      2mb
    </td>
    
    <td>
      Bıos Boot Bölümü
    </td>
  </tr>
  
  <tr>
    <td>
      /dev/sda2
    </td>
    
    <td>
      ext2
    </td>
    
    <td>
      128mb
    </td>
    
    <td>
      Boot bölümü
    </td>
  </tr>
  
  <tr>
    <td>
      /dev/sda3
    </td>
    
    <td>
      (swap)
    </td>
    
    <td>
      512mb
    </td>
    
    <td>
      Swap alanı
    </td>
  </tr>
  
  <tr>
    <td>
      /dev/sda4
    </td>
    
    <td>
      ext4
    </td>
    
    <td>
      Diskin geri kalanı
    </td>
    
    <td>
      Root bölümü
    </td>
  </tr>
</table>

Şimdi parted komutu ile diski bölümlendirmeye başlıyoruz. Parted dışında disk bölümlendirme yapabileceğimiz komutlarda var fakat kullanım kolaylığı için parted&#8217;ı seçtim.

<pre class="wp-block-preformatted"><code>root: parted -a optimal /dev/sda</code> </pre>

Çoğu x86 ve amd64 mimarisi kullanan çoğu disk msdos label ile hazırlanmıştır. Biz gpt kullanacağımız için gpt label ekleyerek devam ediyoruz.

<pre class="wp-block-preformatted">(parted): mklabel gpt</pre>

Diski bölümlendirirken kullanacağımız alanları MB cinsinden yazacağımız için bunu tanımlıyoruz

<pre class="wp-block-preformatted">(parted) unit mib</pre>

Şimdide ilk olarak 2 Mb olarak tanımladığımız Bios Boot bölümü (yani grup) oluşturalım.  
**mkpart primary** diyerek bir bölüm oluşturacağımızı ve bu bölümün primary part olduğunu söylüyoruz, sonrasında bu oluşturacağımız disk bölümünün **başlangıç ve bitiş mb**&#8216;lerini giriyoruz.  
**name 1 grub** satırında ise bu oluşturduğumuz bölüme bir isim veriyoruz. Sonraki satırda bu bölüme flag ekleyip kontrol için print komutunu çalıştırıyoruz. 

<pre class="wp-block-preformatted"><code>(parted)mkpart primary 1 3 </code>
<code>(parted)name 1 grub </code>
<code>(parted)set 1 bios_grub on </code>
<code>(parted)print</code>
  Model: Virtio Block Device (virtblk) Disk /dev/sda: 20480MiB     
  Sector size (logical/physical): 512B/512B Partition Table: gpt    Number   Start      End      Size     File system  Name   Flags  
1        1.00MiB    3.00MiB  2.00MiB               grub  bios_grub </pre>

Print komutundan sonra ilk disk bölümümüzü başarıyla oluşturduğumuzu görüyoruz. Diğer bölümleri de aynı şekilde oluşturalım.

<pre class="wp-block-preformatted"><code>(parted) mkpart primary 3 131 </code>
<code>(parted) name 2 boot </code>
<code>(parted) mkpart primary 131 643 </code>
<code>(parted) name 3 swap </code>
<code>(parted) mkpart primary 643 -1 </code>
<code>(parted) name 4 rootfs</code>
(parted) quit</pre>

4. bölüm olan **root file sistem** bölümüne diskteki kalan alanı vericeğimizi tabloda belirtmiştik. Diskte kalan alanları hesaplamak yerinde bitiş değerine -1 verirsek otomatik olarak kalan alanı bu bölüme ekler.  
Bu işlemlerden sonra print komutu ile doğru olup olmadıklarını kontrol edin.  
Disk bölümlendirmede sıkıntı yok ise **&#8220;quit&#8221;** diyerek parted&#8217;dan çıkalım

Yukarıdaki tabloda sda2 bölümü için ext2, sda4 için ex4 dosya sistemi olacağını belittik. Şimdi bu bölümlere dosya sistemlerini verelim. Fakat bu işlemleri yapmadan parted&#8217;dan quit ile çıktığınızdan emin olun.

<pre class="wp-block-preformatted">root: mkfs.ext2 /dev/sda2 
root: mkfs.ext4 /dev/sda4</pre>

sda3 disk bölümünü swap space olarak kullanıcağımızı belirttik. Bunun için gerekli komutları girelim.

<pre class="wp-block-preformatted">mkswap /dev/sda3
swapon /dev/sda3</pre>

Mount komutu bir depolama cihazını veya dosya sistemini bağlar, erişilebilir hale getirir ve mevcut bir dizin yapısına ekler. Bu yüzden dev/sda4 disk bölümünü root olarak kullanacağımız dizine mount ediyoruz.

<pre class="wp-block-preformatted">root: mount /dev/sda4 /mnt/gentoo</pre>

Mounting işleminden sonra /tmp klasörüne gerekli izinleri veriyoruz.

<pre class="wp-block-preformatted">root: chmod 1777 /var/tmp</pre>

**Stage Tarball İndirme** 

Gentoo stage3 tarball&#8217;u indirmek için tarihimizin dogru olması gerekiyor kontrol için :

<pre class="wp-block-preformatted">date</pre>

Eğer tarih ve saat yanlış ise kendimiz elle düzeltebiliriz. Bunu için date komutunu kullanıyoruz ve sırasıyla Ay-Gün-Saat-Dk-Yıl ekliyoruz.

<pre class="wp-block-preformatted">date 080512172019</pre>

Stage3 tarball&#8217;u /mnt/gentoo konumuna indirmemiz gerekiyor. Bu konuma geçtikten sonra **links** komutuyla gentoo&#8217;nun sitesine girip tarball&#8217;u indirebliriz.

<pre class="wp-block-preformatted">cd /mnt/gentoo
links<code> https://www.gentoo.org/downloads/mirrors/</code></pre>

Siteye girdikten sonra Downloads>amd64>Stage3 şeklinde ilerleyerek tarball&#8217;u bulabilirsiniz. İndirme işlemi bittikten sonra bu sıkıştırılmış tar dosyasını /mnt/gentoo dizinine açıyoruz.

<pre class="wp-block-preformatted"><code>tar xpvf stage3-* --xattrs-include='*.*' --numeric-owner</code></pre>

Sonrasında kontrol için aşağıdaki komutu çalıştıralım. Eğer çıktı olarak klasik root dizini altındaki dosyaları görüyorsak indirdiğimiz dosya ve açma işlemi başarılı demektir.

<pre class="wp-block-preformatted">ls /mnt/gentoo/</pre>

**Compile Ayarlarının Konfigürasyonu**

Gentoo&#8217;yu optimize etmek için gentoo tarafından desteklenen **portage paket yöneticisi** kullanılır. Bu paket yöneticisi konfigürasyonları yapmak için /etc/portage/make.conf dosyasına ihtiyaç duyar.

<pre class="wp-block-preformatted">nano -w /mnt/portage/make.conf</pre>

Bu dosya içerisindeki &#8220;#&#8221; içerisindeki satırlar yorum satırları diğer satırlar ise konfigürasyon için gerekli Flagler ve değişkenlerdir.

**CFLAG ve CXXFLAGS**

CFLAG ve CXXFLAG; gcc, c ve c++ derleyiciler için optimizasyon flaglerini tanımlar. Maksimum performans için her derleyiciye ayrı ayrı tanımlanabilir.

#### Chrooting

**Mirror Seçimi**

Kaynak kodları indirilirken bu paketler bulundukları repolardan indirirlir. Bu repolari mirorlar sayesinde kendimize fiziksel olarak yakındaki repoyu şeçerek bu indirme işlemlerini hızlı bir şekilde indirebiliriz. Kendimize yakın mirror şeçerek işlemlere devam edelim.

`mirrorselect -i -o >> /mnt/gentoo/etc/portage/make.conf`

Mirror seçiminde 2. önemli adım gentoo.conf dosyasıyla gentoo ebuild reposunu yapılandırmaktır. Bu dosya paket repo bilgilerini senkronize edip günceller. 

Eğer repos.conf yok ise oluşturalım 

<pre class="wp-block-preformatted">mkdir --parents /mnt/gentoo/etc/portage/repos.conf</pre>

Gentoo repo configürasyon dosyasını repos.conf dizinine kopyalayalım.

<pre class="wp-block-preformatted"><code>cp /mnt/gentoo/usr/share/portage/config/repos.conf /mnt/gentoo/etc/portage/repos.conf/gentoo.conf</code></pre>

Yeni çalışma ortamına geçmeden önce DNS bilgilerini kopyalamamız gerkiyor. DNS bilgileri /etc/resolv.conf&#8217;ta çalıoşıyor. Bu yüzden bu dosyayı kopyalamamız lazım.

<pre class="wp-block-preformatted"><code>cp --dereference /etc/resolv.conf /mnt/gentoo/etc/</code></pre>

**Gerekli Dosya Sistemlerinin Mountingi**

Birkaç adım sonra root dizinini değiştireceğiz. Bu yüzden yeni çalışma ortamında bazı dizinleri kullanmammız gerekicek. Bu yüzden bu dizinleri yeni çalışma ortamında kullanacağımız konumlara mount ediyoruz. 

<pre class="wp-block-preformatted"><code>root #mount --types proc /proc /mnt/gentoo/proc </code>
<code>root #mount --rbind /sys /mnt/gentoo/sys </code>
<code>root #mount --make-rslave /mnt/gentoo/sys </code>
<code>root #mount --rbind /dev /mnt/gentoo/dev </code>
<code>root #mount --make-rslave /mnt/gentoo/dev </code></pre>

**Yeni Çalışma Ortamına Geçiş (Entering the new environment)** 

Yeni çalışma ortamı demek, dosya sistemimizdeki en üstteki root dizini ( / ) olarak başka bir dizin kullanacağımız anlamına geliyor. Yani birazdan yapacağımız işlemlerden sonra root dizini olarak /mnt/gentoo/ dizinini kullanacağız. 

İlk adım chroot ile yeni root dizininin konumunu veriyoruz. Source komutu ile bazı ayarları belleğe yeniden yüklüyoruz. Sonra bu oturumun chroot ile oluşturulduğunu hatırlatmak için primary promt&#8217;u değiştiriyoruz.

<pre class="wp-block-preformatted"><code>root #chroot /mnt/gentoo /bin/bash </code>
<code>root #source /etc/profile </code>
<code>root #export PS1="(chroot) ${PS1}"</code></pre>

Bu komutlardan sonra &#8220;cd /&#8221; komutu aslında /mnt/gentoo dizinini ifade ediyor olacak. İşlemlerin dogru yapıldının kontrolu için komut satırındaki değişikliğe göz atabilirsiniz. Artık satırın başında &#8220;(chroot)&#8221; ifadesi olması gerekiyor.

İlk adımlarda oluşturduğumuz disk bölümlerine ayrıştırma işlemlerinde /dev/sda2&#8217;yi herhangi bir dosyaya mount etmekdik. Şimdi yeni ortama geçtigimize göre mount işlemini yapabiliriz.

<pre class="wp-block-preformatted">mount /dev/sda2 /boot</pre>

**Portage Konfigürasyonu** 

webrsync 873. port rsync trafiğine izin vermeyen internetler için normal Http bağlantı ile anlık yüklemeler için kullanılır. Eğer kısıtlanmış firewall( restrictive firewall) yoksa bu adımı geçebilirsiniz.

<pre class="wp-block-preformatted">emerge-webrsync</pre>

**Doğru Profil Seçme**

Profiller, gentoo sistemine bir blok inşaa eder(Arayüz gibi). eselect profile komutu ile profilleri görüntüleyebiliriz.

<pre class="wp-block-preformatted">eselect profile list</pre>

Çıkan seçenekler arasında  
[1]default/linux/amd64/17.1 *  
[2]default/linux/amd64/17.1/desktop  
[3]default/linux/amd64/17.1/desktop/gnome  
profilleri göreceksiniz.  
default/linux/amd64/17.1 * > GUI olmadan masaüstü kullanım için gereken profildir. Ben bu profili seçiyorum. Sizde kendi kullanımına uygun şekilde profil seçebilirsiniz.  


<pre class="wp-block-preformatted">eselect profile set 1</pre>

Profil seçiminden sonra sistem için gerekli olan Flagleri otomatik olarak make.conf dosyasına ekler.

**@world set&#8217;i Güncelleme**

Yaptığımız değişikliler ve profil seçiminden sonra eklenen Use flagler için sistemin ortam oluşturması ve güncellenmesi gerekiyor. Bu yüzden aşağıdaki komutu çalıştırıyoruz. **Seçtiğiniz profile göre bu işlem uzun sürebilir.**

<pre class="wp-block-preformatted"><code>emerge --ask&nbsp;--verbose --update --deep --newuse @world</code></pre>

#### Linux Kernel&#8217;in Konfigürasyonu

Tüm dağıtımların kurulduğu temel Linux çekirdeğidir. Çekirdek (core ) kullanıcı programları ile donanım arasındaki alandır. Kernel yapılandırması için kernel source indirmemiz gerekiyor.

<pre class="wp-block-preformatted"><code>emerge --ask sys-kernel/gentoo-sources</code></pre>

İndirdiğimiz kernel source /usr/src içerisine indiriyor. Bu dizindeki linux sembolik link ile linux çekirdeğini point etmesi gerekiyor.

`root #``ls -l /usr/src/linux`

<pre class="wp-block-preformatted"><code>root #ls -l /usr/src/linux</code>
lrwxrwxrwx    1 root   root    12 Oct 13 11:04 /usr/src/linux -&gt; linux-3.16.5-gentoo </pre>

**Manuel Konfigürasyon**

Kernel konfigürasyonu elle yapılandırmamız gerekiyor. Bir çekirdek elle yapılandırıldığında sistemi bilmekönemlidir. Çoğu bilgi, lspci komutunu içeren sys-apps / pciutils ile toplanabilir.

<pre class="wp-block-preformatted"><code>emerge --ask sys-apps/pciutils</code></pre>

Konfigürasyon ekranına geçmek için aşağıdaki komutları çalıştırmamız gerekiyor. 

<pre class="wp-block-preformatted"><code>cd /usr/src/linux
make menuconfig</code></pre>

Bu komutları çalıştırdığımızda Lİnux Kernel Config ekranına geçmiş olmamız gerekiyor. Burda bir çok seçenek mevcut. Hangi seçeneklerin ne olduğu ve neleri seçiceğimize [Gentoo Handbook&#8217;tan Activating required](https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/Kernel) options kısmında bakabilirsiniz

Yaptığımız kernel konfigürasyonunun derlenip indirmesini yapıyoruz. 

<pre class="wp-block-preformatted"><code>make && make modules_install</code></pre>

Kernel derleme işlemini bitirdiğinde, imajları /boot/ dizinine kopyalıyoruz.

<pre class="wp-block-preformatted"><code>make install</code></pre>

**Initramfs** (Opsiyonel)

Bir initramfs olmadan, dosya sistemlerini kurmaktan sorumlu olan araçların bu dosya sistemlerinde bulunması gereken bilgilere ihtiyaç duyması nedeniyle sistemin düzgün şekilde açılmaması riski vardır. Bir initramfs, gerekli dosyaları, çekirdek önyüklemesinden hemen sonra, ancak denetim init aracına teslim edilmeden önce kullanılan bir arşive çeker. İnitramfs üzerindeki komut dosyaları daha sonra sistem önyüklemeye devam etmeden önce bölümlerin düzgün şekilde monte edildiğinden emin olacaktır.

İnitramfs&#8217;i indirip oluşturalım.

<pre class="wp-block-preformatted"><code>emerge --ask sys-kernel/genkernel</code>
<code>genkernel --install initramfs</code>
<code>genkernel --lvm --mdadm --install initramfs</code></pre>

**Genkernel Kullanımı** 

Eğer manuel kernel konfigürasyonu zor geldiyse genkernel sayesinde otomatik olarak gerekli konfigürasyonları yapar.

<pre class="wp-block-preformatted"><code>emerge --ask sys-kernel/genkernel</code>
<code>genkernel all</code></pre>

**Sistem Araçlarını İndirme**

Stage3 arşivinde bazı araçlar eksik çünkü birkaç paket aynı işlevi sağlıyor. Hangilerinin yükleneceğini seçmek kullanıcıya kalmıştır. Karar verilecek ilk araç, sistem için kayıt olanakları sağlamak zorundadır. Unix ve Linux mükemmel bir kayıt tutma özelliğine sahiptir. Gerekirse sistemde olan her şey günlük dosyalarına kaydedilebilir. Bu sistem kayıt cihazında gerçekleşir. Birkaç çeşit sistem logger&#8217;ı var. Biz sysklogd&#8217;yi kurucaz. 

<pre class="wp-block-preformatted"><code>emerge --ask app-admin/sysklogd</code>
<code>rc-update add sysklogd default</code></pre>

**Boot Loader** 

Sistem ilk açılıştan yani bilgisayarın düşmesine bastığımız anda sistem nasıl ilerleyeceğini bilemez. Bunun için Boot Loader&#8217;lar kullanılır. Biz boot loader olarak **grub2**&#8216;yi kurucaz. Bu işlem biraz uzun sürebilir.

<pre class="wp-block-preformatted"><code>emerge --ask&nbsp;--verbose sys-boot/grub:2</code></pre>

Şimdi Grub2 için gerekli olan dosya sistemi /boot/grub dizinini indiricez.

<pre class="wp-block-preformatted"><code>grub-install /dev/sda</code></pre>

/etc/default/grub dosyası ve /etc/grub.d konumunda grub2 için yapılandırma yapalım.

<pre class="wp-block-preformatted"><code>root #grub-mkconfig -o /boot/grub/grub.cfg</code>
Generating grub.cfg ... Found linux image: /boot/vmlinuz-3.16.5-gentoo Found initrd image: /boot/initramfs-genkernel-amd64-3.16.5-gentoo done </pre>

Diğer alternatif boot loader&#8217;lar hakkında [burdan](https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/Bootloader) bilgi elde edebilirsiniz 

chroot ortamından çıkıp mount ettiğimiz işlemler umount ile kaldırıyoruz. Sonra sistemimizi reboot edebiliriz. 

<pre class="wp-block-preformatted">(chmood)exit
<code>root cd </code>
<code>root ~#umount -l /mnt/gentoo/dev{/shm,/pts,} </code>
<code>root ~#umount -R /mnt/gentoo </code>
<code>root ~#reboot</code></pre>

Artık Gentoo kullanım için hazır. Root olarak sisteme girip kullanıcı oluşturup gerekli izinleri verip kullanıcımız ile devam edebiliriz. Sonrasında artık kullanammıza gerek kalmayan Stage3 tar dosyasını root dizininden silebilirsiniz.
