---
title: "LLDP ve CDP Protokolleri"
date: 2019-12-05T14:00:58+00:00
layout: post
categories:
---


## LLDP ve CDP Protokolleri


# LLDP (Link Layer Discover Protocol)
Zamanla ihtiyaçtan dolayı network cihazlarının artması yüzünden çalışsan sistemler ve topolojilerde karmaşık bir hal almıştır. 
Bu yüzden zamanla network cihaz sağlayıcıları kendilerince network keşfi için bazı çözümler/protokller üretmeye başladı. Bu protokoller çoğunlukla SNMP ile uyumlu çalışacak şekilde geliştirildi.
Günümüzün sürekli genişleyen ağ yapıları yönetimi basitleştiririldi. LLDP (Link Layer Discovery Protocol - Bağlantı Katmanı Keşif Protokolü), şu anda ağ topolojileri ve ağ cihazlarını keşif protokolü olarak kabul edilmiş durumdadır. 
LLDP; IEEE tarafından 802.1AB ve 802.3 standartlarına uygun şekilde geliştirilmiş bir protokoldür.
LLDP kullanılan ağ cihazının markasından bağımsız olarak çalışabilmektedir.
LLDP açık kaynak bir protokoldür ve bu sayede ileri zamanlarda yeni gelecek teknolojiler içinde uygun şekilde kullanılabilir.
LLDP'nin temel amaçı fiziksel ağ içerisinde ethernet bağlantısı olan cihazlar hakkında bilgi toplamak ve ağ hakkındaki bilgileri depolamak için geliştirilmiş bir komşu keşif protokolüdür.
LLDP, cihazların birbirlerinin kimlik bilgilerini, yeteneklerini, özel yapılandırma bilgilerini tespit edebilmesini de sağlar.
MIB (Managament Information Base) modülleri tanımlanır. 
LLDP, yönetim bilişim temeli aracılığı ile sorgu yapar ve diğer cihaz da bu sorguya cevap döner. 
LLDP'nin genel mantığı budur. 
LLDP yönlendiricileri, köprüleri, tekrarlayıcıları, WLAN (Kablosuz Yerel Ağ Bağlantısı) erişim noktalarını, IP telefonları, ağ kameraları veya üretici önemi olmaksızın herhangi bir LLDP uyumlu cihazı keşfetmek için kullanılabilir.

# CDP (Cisco Discovery Protocol)
CDP sadece cisco cihazlarda çalışan ve  komşuluk bilgilerini (direkt olarak kendine bağlı olan cihazların bilgilerini) paylaşan protokoldür.
CDP; Yönlendirici (Router), Anahtarlayıcı (Switch),  Erişim Sunucusu (Access Server), Köprü (Bridge) gibi ağ cihazlarının hepsinde kullanılır.
Bu protokol sayesinde ağ üzerinde bulunan bir cihazın komşu cihazlarının yerleri direk olarak tanımlanır. CDP  kullanılarak sadece hangi cihaz olduğu değil aynı zamanda aygıt adı (device id), açık olan arayüzler (interface), cihazın portlarında bulunan ip adresleri, cihazların fonksiyonel kapasiteleri ve cihazların platformları, cihazda kullanılan işletim sistemi sürümü de rahatlıkla görülebilir. Bunun olması için cihazların CDP'nin açık olması gerekir ki varsayılan olarak açık gelir.
İstenirse bu özellik kapanatilabileceği gibi belirli portlar üzeeirnde de kapatılabilir.
Çalışma mantığı LLDP ye benzer şekildedir. CDP'yi kapatılmamış olan her cihaz kendi ağında çoklu gönderim (multicast) olarak bilgilerini yayınlar. Bu şekilde diğer komşu cihazlar da bu bilgilere erişir. 
CDP protokolünden alınan bilgileri cisco cihazlar kullanmaz. Protokolün amacı ağ yöneticilerinin işini kolaylaştırmaktır.
CDP ile toplanan bilgiler ile topoloji oluşturulabilir, cihaz durumları, interfece durumları vs gibi bilgilere ulaşabilir ve yönetebilirsiniz.
Aşağıdaki komutlar sayesinde Cisco cihazların topladıkları cdp bilgilerini kontrol edebilirsiniz.
show cdp neighbours
show cdp neighbours detail
show cdp traffic
