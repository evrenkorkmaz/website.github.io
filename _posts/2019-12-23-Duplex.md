---
title: "Bilgisayar Ağlarında İletim Türleri (Duplex Yapılar)"
date: 2019-12-23T14:00:58+00:00
layout: post
categories:
---


# Bilgisayar Ağlarında İletim türleri (Full-Duplex Half-Duplex ve Simplex Terimleri)

İletim türleri(Transmission Mode) basit olarak iki cihaz arasındaki iletişimin nasıl olacağını tanımlayan bir terimdir. 
Bu iletim türleri genel olarak noktadan noktaya (P2P) iletimi tanımlar ve ihtiyaca göre kullanılır. Sadece network iletişimi değil telekomikasyodada bu iletim türleri kullanılır.
3 türü vardır: 
- Full Duplex
- Half Duplex
- Simplex

# Simplex
Simplex modunda tek yönlü bir iletişim vardır. İletişim her zaman bir cihaz veriyi yollar diğeri sadece veriyi alır.
Örnek vericek olursak bir cihazdan yazdığımız bir mesaj vs. diğer cihaza iletilecek ve iletilen cihaz sadece bu mesajı okuyabilecek. 
Mesajı okuyan cihazdan herhangi bir dönüş, iletim olmaz. İletim her zaman tek yönlüdür ve tersine dönmez.
Radyolar örnek olarak gösterilebilir. Bir radyo her zaman yollanan sinyalleri alır ve hiçbir zaman yayın yapmaz.

# Half Duplex
Bu modda Simplex'te olduğu gibi tek taraflı bir iletişim vardır. Fakat Simplex'ten farklı olarak iletim yönü değişiklik gösterebilir. 
Örnek olarak bir cihazdan mesaj yollancağı zaman diğer cihaz bu mesajı bekler ve herhangi bir iletim yapamaz. 
Fakat belli koşullar sağlandığı zaman iletişim yönü değişebilir. Yani mesajı ileten cihaz mesaj yollamayı bırakıp gelecek olan mesajı bekler.
Mesaj bekleyen cihazda mesaj yollamaya başlar. 
Kısacası her zaman bir cihaz mesaj yollar diğeri bekler ve iletim yönü değişebilir.
Bas-Konuş Telsiz cihazları bu iletim türüne verilecek örneklerden birisi.

# Full Duplex
Full Duplex ise aynı şekilde iki taraflı bir iletişimde olur ama bu modda her iki tarafta aynı anda mesaj alıp mesaj yollayabilir. 
Örnek olarak cep telefonlarında arama yaptığımızda her iki tarafta aynı anda birbirleriyle iletişim kurabiliyor.
Yada herhangi bir mesajlaşma uygulamasında hem mesaj yollayıp aynı anda mesaj alabiliriz.
Bu yapıya Full Duplex iletişim denir.

