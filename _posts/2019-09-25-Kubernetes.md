---
title: "Kubernetes Nedir?"
date: 2019-06-27T14:00:58+00:00
layout: post
categories:
---

#Kubernetes Nedir?

Kubernetes, Google tarafından Go dili ile geliştirilen "Cloud Native Computing Foundation" tarafından desteklenen, mevcut konteyner haline getirilmiş uygulamaları 
otomatik deploy eden, sayılarını arttırıp-azaltmak gibi işlemleri yapabildiğimiz bir konteyner kümeleme (conteyner cluster) aracıdır.
 
[![kuber1.png](https://i.postimg.cc/T3V8cjbJ/kuber1.png)](https://postimg.cc/jDSkx7ZD)
.
Bu teknoloji kullanılarak uygulamalarımızı mikro servis mimarisi aracılığıyla daha kolay yönetilebilir, sorunları daha kolya çözebilir ve en iyisi 
bu yapıları (oluşturduğumuz konteynerları), kubernetes  taşınabilir bir ortam sağladığı için kolayca taşıyabiliriz. 
Docker'da aslında bu işlemleri yapabilir fakat kubernetes bazı ihtiyaçlar doğrultusunda ortaya çıkmıştır. Operasyonel işi zor ve maliyetli olan scaling,rollback,
auto deploymen secure discovery gibi pek çok işlemi kolayca ve tek bir elden halletme imkanı sağlar.

###Kunbernetes Yapısı

[![node-yap-s.png](https://i.postimg.cc/qq6BBt5D/node-yap-s.png)](https://postimg.cc/9wVHJfVy)


Kubernetes yapısında "master" ve "minion node"lar vardır. Minion Node'ların içerisinde "Pod"lar. Podların içerisinde "Container"lar vardır.
Bu yapı kendi içerisinde Overlay denen bir network ile haberleşir.

[![node.png](https://i.postimg.cc/fbS8KD1n/node.png)](https://postimg.cc/3y7jr5Kn)
.

##Master Node Yapısı 


[![master-node.png](https://i.postimg.cc/VkwJLRBx/master-node.png)](https://postimg.cc/xcsf6LZ5)
.

###API Server 

Master Node'a gelen bütün Rest Requestlerin yönetilmesinden sorumludur. Aslında clusterın(kümenin) beynidir.

###Control Manager 

Temel olarak bir denetleyicidir. Clusterın (kümenin) durumunu API ile izler ve istendiğinde, geçerli durumu istenen duruma hareket ettirmek için gerekli değişiklikleri yapar.

###Scheduler

Bir Pod'un hangi node üzerinde çalışacağına karar verir ve Kubeleti tetikler ve ilgili Pod ve içerisindeki konteyneri oluşturur. Yeni bir pod oluşturma isteğine karşı API serveri dinler.

###etcd

Tutarlı ve izlenebilir bir key value store(nosql database) denebilir.

#Kubernetes Node'ların (Minion Node) Yapısı

[![minion-node.png](https://i.postimg.cc/qRWRfXvZ/minion-node.png)](https://postimg.cc/phYxzj8D)
.

###Kubelet

Node üzerinde çalışan bir ajandır. API'dan gelen istekleri bekler, ilgili docker servisi ile konuşarak pod'u ayağa kaldırır ve bilgiyi API'ya iletir.

###Kube-Proxy

Kubernetes networkü. Podlarla ilgili IP adresleri proxy ile atanır. Pod içerisindeki konteynerlar aynı IP adreslerini kullanır.

###Container Engine

Konteyner yönetimi yapar. İmajları ilgili Registery üzerinden çeker ve konteynerların start-stop olmasını sağlar.

###POD

[![pod.png](https://i.postimg.cc/JzSdkstC/pod.png)](https://postimg.cc/LJBTd6zx)
.
Konteynerların çalışma alanıdır. Podlar içerisinde birden fazla konteyner çalışabilir. Kubernetesin çalışma mantığı gereği Deploy isteği geldiğinde yeni bir pod oluşturur.
Bu pod'un çalıştığı bilgisi geldğinde eski pod öldürülür. Bu durumda pod içerisinde birden fazla konteyner var ise bu durumdan diğerleride etkileneceğinden, pod içerisinde 1 konteyner çalıştırılması önerilir.
Pod öldüğünde geri kalkmaz. Aynı imajdan yerine yeni bir pod kaldırılır.

##Kuberbetes ile ilgli bazı terimler.

###Replicaset

Bir pod'dan kaç tane oluşturulacağını (kaç kopya olacağı)belittiğimiz bölüm. İstedğimiz anda elimizdeki podları çoğaltabilir veya azaltabiliriz.

###Namespace

Yukarıdada belirttiğimiz gibi Kubernetes, konteyner kümelerinden oluşur(podlar gibi). Bu kümelere isim ataması yapılan bölüm.

###Service 

Podlara gelen istekleri karşılayıp arka tarafa(podlara) gönderen katman.

###Kubernetes Çalışma mantığı

[![kuber-calima.png](https://i.postimg.cc/85jfWzYM/kuber-calima.png)](https://postimg.cc/ZBzqz445)
.

1-  Kubectl(kubernetes client) isteği API server ‘a iletir.
2-  API Server isteği kontrol eder etcd ‘ye yazar.
3-  etcd yazdığına dair bilgilendirmeyi API Server’a iletir.
4-  API Server ,yeni pod yaratılacağına dair isteği Scheduler ‘a iletir.
5-  Scheduler, pod ‘un hangi server’da çalışacağına karar verir ve bunun bilgisini API Server’a iletir.
6-  API Server bunu etcd ye yazar
7-  etcd yazdığına dair bilgiyi API Server’a iletir.
8-  API Server ilgili node’daki kubelet’i bilgilendirir
9-  Kubelet,Docker servisi ile ilgili docker soketi üzerindeki API’yi kullanarak konuşur ve konteyner’ı yaratır.
10- Kubelet ,pod’un yaratıldığını ve pod durumunu API Server’a iletir.
11- API Server pod’un yeni durumunu etcd’ye yazar.

##Minikube

Kendi fiziksel bilgisayarımızda kubernetesi denememiz için sanallaştırma yapan araçtır. Bu araç sayesinde kendi bilgisayarımız üzerinde kubernetesi kurup üzerinde işlemler yaparak nasıl kullanıldığını öğrenebilir yada yaptığımız cluster'ı  kendi bilgisayarımızdan denemelerini-testlerini yapmamıza olanak sağlar.
Bu sanallaştırma için virtualbox,KVM gibi sanallaştırma yazılımlarımızın olması gerekiyor. 
**minikube start** komutu ile sanalaştırma işlemini başlatabiliriz.

