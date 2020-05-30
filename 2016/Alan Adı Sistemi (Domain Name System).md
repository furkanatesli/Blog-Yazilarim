---
Başlık: Alan Adı Sistemi (Domain Name System)
Paylaşım Tarihi: 06.05.2016
Etiketler: Alan adı, DNS, Domain Name System
---

## Alan Adı Sistemi (Domain Name System)

DNS internet kaynakları için hiyerarşik bir adlandırma sistemidir. İnternet için adres defteri olarak görülebilir. İstemci/sunucu modelini kullanan dağıtık bir veri tabanı sistemi olarak korunur. Çözücüler bilgi için veri tabanını sorgular. Ad sunucuları yerel olarak depolanmış kaynak kayıtlarından elde edilen bilgiler ile yanıt verir.

**DNS Ad Alanı(DNS Namespace)**

![](medya/Alan-Adı-Sistemi-(Domain-Name-System)/1.png)

DNS Ad alanı kök, düğümler ve alt ağaçlar ile ters bir ağaç olarak görülebilir. Her bir düğüme bir metin etiketi atanır. Her alt ağaç DNS veri tabanının bir kısmını temsil eden bir etki alanı veya bir alt alan olarak görülebilir. Alan adları en fazla 63 karakter olabilir.Üst seviye alan adlarının yönetimi InterNIC tarafından yapılmaktadır. ISO kodları ile tanımlanmış ülke adlarının yönetimi ülkelere göre değişiklik gösterir. Türkiyenin ISO kodu olan tr üst seviye alan adının yönetimi Orta Doğu Teknik Üniversitesi tarafından yapılmaktadır.tokatteknikmyo.gop.edu.tr örneğindetokatteknikmyo.gop.edu.tr alan adı Gaziosmanpaşa Üniversitesi sunucuları üzerinde .gop.edu.tr alan adı ODTÜ sunucuları üzerinde.edu.tr alan adı yine ODTÜ sunucuları üzerinde.tr alan adı ise Kök sunucuları üzerinde tutulmaktadır.Dünyada 13 adet kök sucusu bulunmaktadır.

**DNS Çözümlemesi**

DNS’in yapısının anlatırken dağıtık bir veri tabanı şeklinde oluşturulduğunu ve düğümlerin birbirleriyle istemci sunucu mantığı ile konuştuğunu söylemiştik. Bu işlevi yerine getiren programlara alan adı sunucusu adı verilir. Her alan adı sunucusu bir veya birkaç alan adı bilgisini tutar ve bu alan adları için en yetkili sunucudur. Diğer alan adları için sorgularda bu alan adları için en yetkili alan adı sunucularını bulmaya çalışır.

**DNS çözümlemesi birkaç kademede incelenebilir.**

Gaziosmanpaşa Üniversitesi alan adı sunucusunu kullanan bir istemcinin www.gop.edu.tr adresini sorguladığı durumda sunucu kendi veri tabanında tanımlı olan bu adresi istemciye döndürecektir. Bu Gaziosmanpaşa Üniversitesi alan adı sunucusu www.gop.edu.tr alan adı altında bulunan tanımlar için en yetkili sunucu olduğu için bu şekilde gerçekleşmiştir.

![](medya/Alan-Adı-Sistemi-(Domain-Name-System)/2.png)

Görüldüğü gibi istemci www.gop.edu.tr adresini bu alan adı için en yetkili durumdaki alan adı sunucusunda sorgulanmış ve bu adrese tanımlanmış IP adresi cevap olarak döndürülmüştür.Aynı sorguyu www.gop.edu.tr için herhangi bir bilgiye sahip olmayan ns.x.net adlı alan adı sunucusunu kullanan bir istemcinin yaptığı durumu inceleyelim. 
ns.x.net kendisine sorulan www.gop.edu.tr adresi hakkında herhangi bir bilgiye sahip olmadığı için kendi veri tabanında tanımlı olan kök seviye alan adı sunucularına bu adresi sorar. Kök sunucusu da www.gop.edu.tr için kesin bir bilgiye sahip değildir ancak .tr üst seviye alan adının ns1.metu.edu.tr ad sunucusu tarafından kontrol edildiğini bilmektedir. Bu yüzden sorguyu ns.x.net’e ns1.metu.edu.tr ad sunucusu üzerinde yapılması bilgisini iletir. ns.x.net bu kez www.gop.edu.tr adresini ns1.metu.edu.tr ad sunucusuna sorar. Bu sorguda da kesin bir bilgi yoktur ama .edu.tr ikinci seviye alan adının ns2.metu.edu.tr ad sunucunun kontrol ettiğini bilmektedir. Bu sorguyu ns.x.net’e ns2.metu.edu.tr ad sunucusu üzerinde yapılmasını bildirir. ns.x.net www.gop.edu.tr adresini ns2.metu.edu.tr ad sunucusuna sorar. Ancak bu sunucuda www.gop.edu.tr adresi için kesin IP adresini bilemeyecek ve sorgunun www.gop.edu.tr alan adı sunucusu olan ns2.gop.edu.tr yönlendirilmesini bildirecektir. Son olarak ns.x.net www.gop.edu.tr adresini ns2.gop.edu.tr üzerinde sorgulayacak ve veri tabanında bulunan www.gop.edu.tr için tanımlı olan 193.140.180.2 IP adresini döndürecektir. IP adresine ulaşan ns.x.net de kendi istemcisine bu bilgiyi iletecektir.

![](medya/Alan-Adı-Sistemi-(Domain-Name-System)/3.png)

**DNS Önbellek(DNS Cache)**

DNS Çözümlemesinde gördüğümüz gibi isim çözümleme işlemi için çok sayıda ad sunucusuna sorgu sormamız gerekir ve bu bazen oldukça vakit alan bir işlem haline dönüşebilir. Bunun engellenmesi için ve sorgunun kısa sürede cevaplanması için ad sunucularında önbellek bulunur. Önbellek sayesinde daha önce sorgulanan alan adları sunucuda saklanır ve yeni sorguda diğer sunucularla iletişime gerek kalmaz. Ancak alan adında son sorgudan sonra değişiklik yapılmış olabilir yani önbellekte tutulan bilgi artık güncel olmayabilir bu durumda sunucular istemciye hatalı bilgi iletebilir. Bu ancak belli bir noktaya kadar kabul edilebilir bir durumdur. Bu gibi durumlarda Yaşam Süresi(Time To Live-TTL) devreye girer. Her alan Adının tanımlanmasında bu alan adı için geçerli TTL değeri belirtilir. Bu alan adından sorgulanan bilgiler bellekte TTL süresince tutulduktan sonra güncelliğini yitirdi kabul edilir.
