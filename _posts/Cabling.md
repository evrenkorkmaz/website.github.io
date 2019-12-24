---
title: "Network Kablolama Standartları"
date: 2019-11-24T14:00:58+00:00
layout: post
categories:
---



# Network Kablolama Standartları 

Network yapılarında kullanım alanı ve ihtiyaç türlerine göre farklı kablo türleri kullanılır. 
Bu ihtiyaçlar değerlendirilirken network büyüklüğüne, topolojiye ve altyapıya göre değerlendirilir.

Genel olarak 4 çeşit kablo türü vardır.
- Coaxial
- Shielded Twisted Pair (STP)
- Unshielded Twisted Pair (UTP)
- Fiber Kablo 

# Coaxial Kablo 
Ortasında bir adet bakır kablo geçen kablo türüdür.
Bu bakır kablo ile trafındaki plastik yapıyla arasında metal bir izolayson malzemesi vardır.
Bu metal korumanın konma nedeni cihazlardan yada başka kablolardan oluşabilecek parazitleri önlemek içindir.
Genel olarak ses ve görüntü iletimi için kullanılır.

# Shielded Twisted Pair (STP)

Twisted Pair Kablolar ismindende anlaşılacağı gibi birbire satılmış çiftler halindeki kablo türüdür. Genellikle 8 veya 12'li olurlar.
Kablolar bir çok dış etkenden etkilenebilir. Bunlardan bir taneside bütün kablolardan geçen elektrik sinyallerinden oluşan manyetik alan yüzünden bağlantının etkilenmesi.
Bu yüzden kablolar çift şekilde birbirlerine sarılır. Birbirlerine sarılan kablolar manyetik alanı sıfırlar (Fizikteki sağ el kuralı) ve bu durumu ortadan kaldırır.
Shielded yapısı ise her kablo çiftini ayrıca metal bir koruma ile kaplanmasıdır. Buda kablonunun kullanacağı alanda yüksek manteyik alandan etkilenmesi durumu varsa tercih edilir.

# Fiber Kablo

Fiber kablo diğer kablolara benzer bir yapısı vardır fakat iletimi diğer kablolar gibi bakır tel üzerinden elektrik sinyalleriyle değil cam bir yapı içerisinden ışık sinyalleri yollayarak yapar.
Kablo 3 kısımdan oluşur. 
Çekirdek:  ışıgın iletildiği kısım
Cam Kılıf: Işıgın dışarı cıkmasını önleyen kısım
Koruyucu kılıf: Kabloyu dış etkenlerden koruyan kısım.
2 çeşit fiber kablo vardır.
Bunlar :
Single Mode 
Multi Mode
Bu iki modda aynı kablo yapısına sahiptir. Aralarındaki fark SM'de sadece tek bir ışık sinyali vardır. Multi Mode(MM)'de ise kablo içerisinde birden fazla ışık sinyali olabilir.
SM kablolar uzun mesafeler için idealdir. 100km'ye kadar 10Gbps veri iletimi yapabilirler. MM kablolara göre çekirdek kısmının çapı daha küçüktür.
MM kablolar ise daha kısa mesafelerde kullanılır. MM kablolardan yollanan ışık sinyalleri birden fazla olduğundan birden çok yol üzerinden iletilir. 550-1000 metre arasında 10 Gbps veri aktarımı yapabilir.

# Unshielded Twisted Pair (UTP)
STP kablolara benzer bir yapısı vardır. STP için geçerli özellikler bu türdede geçerlidir. STP kablolardan farklı her çift üzerinde ekstra bir koruyucu yoktur.
Kullanımı en yaygın olan kablo türüdür.
UTP kablolar kendi içerisinde CAT1-2-3... olarak sınıflandırılır.
CAT1: Genellikle telefon kablolarında kullanılır. (Ses iletimi için)
CAT2: 4 Mbps'e kadar hız destekler. Genellikle token ring networklerde kullanılır.
CAT3: 10 Mbps hızında veri aktarabilir.
CAT4: 16 Mbps hızında veri aktarabilir.
CAT5: Ethernet gibi bilgisayar ağları için yapısal kablolama sağlar ve ses ve görüntü sinyallerinin iletiminde kullanılır. Cat5 kabloları 100 MHz’e kadar yapılması gereken testleri karşılar ve 100 Mbps'e kadar bant genişliği sağlar.
CAT5 kabolar 100 metreden sonra veri kaybı yapar. Önerilen ise 90 metredir. Bu yüzden 100 metreden daha uzun kullanılacaksa araya  repeater yada bir Switch gibi donanımlar eklenmelidir.
CAT5 kablolardaki belirli bir mesafeden sonra yaşanan frekans aralığı düşmesi sorununu azaltmak ve bant genişliğini artırmak amacıyla Cat5e kablolar üretilmiştir. Aynı frekansı (100 MHz)  ileten Cat5e kabloların iç direnci daha düşüktür ve Cat5’e göre daha sert yapıdadır.
CAT6: 100 metreye kadar 1 Gbps hız destekler. CAT5 kablonun geliştrilmiş hali olduğu için aynı özellikler bunlar içinde geçerli.
CAT7: 100 metreye kadar 10 Gbps hız destekler. Aynı şekilde Cat5-6 kablo özellikleri dahil olmak üzere bunların geliştirilmiş halidir.

#Kablolama Türler ( Crossover - Düz )
İki bilgisayar arasında ağ bağlantısı kurulmak istendiğinde (ağ paylaşımı yada iki bilgisayar arasında veri transferi) yapılması gereken bağlantı tipine cross bağlantı denir.Eğer modeminizden bilgisayarlara dağıtım yapıyorsanız DÜZ bağlantı denilen standart bağlantıyı kullanmanız gerekir.
İki çeşit kablolama vardır. Bu çeşitlere A-B olarak isimlendirlir.
Burda çeşitliliğe sebep olan kısım kablo sıralamalarının farklı olmasıdır.
Mantık olarak kabloların iki ucuda aynı sıralamayla çakıldığında doğru bir şekilde bağlanabiliriz. Fakat bazı durumlarda kablonun bir ucu A tipi, diğer ucu B tipi olduğu zaman bu sıralamlarda değişiklik yapmamız gerekiyor.
A-tipi bağlantı:
1 nolu pin : yeşil-beyaz
2 nolu pin : yeşil
3 nolu pin : turuncu-beyaz
4 nolu pin : mavi
5 nolu pin : mavi-beyaz
6 nolu pin : turuncu
7 nolu pin : kahverengi-beyaz
8 nolu pin : kahverengi

B-tipi bağlantı:
1 nolu pin : turuncu-beyaz
2 nolu pin : turuncu
3 nolu pin : yeşil-beyaz
4 nolu pin : mavi
5 nolu pin : mavi-beyaz
6 nolu pin : yeşil
7 nolu pin : kahverengi-beyaz
8 nolu pin : kahverengi

 
