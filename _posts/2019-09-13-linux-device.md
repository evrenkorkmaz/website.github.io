---
title: Linux Device Types
date: 2019-07-14T21:04:00+00:00
layout: post
categories:
---

# Linux Device Types

Types: 
 - Socket
 - Block
 - Pipe
 - Chracter

### Chracter Device 

Bu device data transferi için kullanılır. Tek seferde bir karakter yollar. /dev/null gibi bir sürü karakter device vardır.
Bu cihazlar fiziksel olarak makineye bağlı değillerdir, fakat işletim sisteminin daha işlevsel çalışmasını sağlar. 

**Chracter Device Örnek**

 - /dev/null : Unix benzeri işletim sistemlerinin aygıtlar dizininde bulunan bir stream dosyasıdır. Bu belgeye yazılan her tür ve herhangi miktarda olan veriyi sistem yok sayar ve yazan işleme EOF(end of file) gönderilir. Bu açıdan /dev/null bir kara deliğe benzetilir

 - /dev/zero : İçinde sonsuz adet sıfır bulunan cihazdır. Genellikle dosya içlerine okuma ve yazma testleri için kullanılır. 


### Block Device 

Bu cihazlar data transferi yapar. Fakat büyük sabit boyutlu dosyalarda çalışır. Block cihazların arayüzü gibi birşey. Fiziksel olarak takılan harddisk vb. için yazılabilecek dosya sistemi(blok olarak tek seferde 512 mb yazmak gibi) yazılır.

### Pipe Device 

2 veya daha fazla prosesin birlikte çalışması için iletişimi sağlar. Karakter devicelara benzer. Çıktıyı bir aygıta göndermek yerine çıktıyı başka bir işleme gönderir.

### Soket Device 

Prosesler arası iletişimi kurmayı sağlar. Pipe device gibi fakat tek seferde çok işlem ile iletişim kurabilir.



 















 



















