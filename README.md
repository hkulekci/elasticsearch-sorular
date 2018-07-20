# Elasticsearch Sorular

#### Elasticsearch'de PDF veya TXT file indexlemek ve indexlediğim PDF veya TXT dosyasının içindeki herhangi bir kelime ile arama yaptığımda listelemek istiyorum bu mümkün mü?
 > Evet Mümkün. Bunun için Apache Tika tabanlı [Ingest Attachment Processor](https://www.elastic.co/guide/en/elasticsearch/plugins/master/ingest-attachment.html) eklentisini kullanabilirsiniz. Eklenti uyumlu binary dosyaları Elasticsearch üzerinde aranabilir olarak bir alan üzerinde tutulmasını sağlıyor. Daha sonra bu alan üzerinde analiz işlemleri yaparak arama sonuçlarında listelenmesini sağlayabilirsiniz.

####	Bir MySQL veritabanını Elasticsearch'e nasıl atarız?
 > MySQL'den verileri taşımak için "Bulk Insert" metodunu kullanan bir script yazabilirsin. Bunun için herhangi bir dilde komut satırından deamon olarak çalışan bir sistem kurabilirsin. Eskiden bunun için plugin'ler vardı river diye ama yeni sürümlerde yok. Ayrıca Logstash'de odbc eklentisi ile bir mysql bağlantısı açıp sorgu ile belirli aralıklarla veri aktarımı da yapabiliyorsun.

#### Index'teki verilerden asıl veriye nasıl geçiş yaparız?
 > Index'teki veriler zaten asıl veriler. Inverted index senin yönetmen gereken bişey değil. Onu ES kendisi yönetiyor. Senin ayrıca bir şey yapman gerekmiyor. Sen verileri kaydederken ES bir kısmını bir inverted index'e verinin kendisini eğer sen aksi bir durum belirtmediysen dosya sistemine kaydediyor.

#### Inverted index ve index farkı nedir? Bunlar ayrı ayrı mı oluşuyorlar? Ya da ikisi de aynı anda mı oluşuyor?
 > Index verilerin tutulduğu veritabanı gibi düşünebilirsin. Birbirine benzer veriler index'lerde tutuluyor. Inverted index ise senin hızlı arama yapabilmen için bir arama yapısı.

#### Biz bir sorgu çalıştırdığımızda, elasticsearch ilk önce inverted index'e bakıp sonra index'e mi geçiş yapıyor? Yoksa direk index'te mi arıyor? 
 >  Index'i verilerin tutulduğu veritabanı gibi düşünebilirsin. Birbirine benzer veriler index'lerde tutuluyor. Inverted index ise senin hızlı arama yapabilmen için bir arama yapısı.

#### Aramalarda Türkçe karakterler ile arama yaptığımda eşleşmiyor ya da "Çiğdem" ile arama yaparken "cigdem" de gelsin "çiğdem" de gelsin istiyorum olmuyor. Bunu nasıl yapabilirim.
 > Bunun için ilk olarak [lowercase filter](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-lowercase-tokenfilter.html) kullanman gerekiyor. Bu filtreyi oluştururken `language` parametresine `turkish` değeri vermen gerekiyor. Diğer taraftan Ç ile arama yaparken C de gelsin istiyorsan [Ascii Folding](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-asciifolding-tokenfilter.html) kullanman gerek. Bu da Ascii olmayan karakterleri Ascii benzerleriyle değiştiriyor. Böylelikle mobil araçlardan yapılan aramalarda da sonuç dönebiliyor. 

#### Verileri Kibana tarafına nasıl geçirebilirim?
 > Verileri Kibana'ya geçirmek diye bir durum yok. Veriler her zaman ES'de duruyor. Kibana sadece ES'den okuyor ve görsel hale getiriyor. Yani Kibana'da verilerinizi göstermeniz için ekstra bir çaba harcamanız gerekmez. Sadece kibana ES url adresi olarak cluster adresinizi verin ve Kibana'yı açın. Daha sonra Kibana size zaten hangi index üzerinde işlem yapmak istediğinizi soracaktır. Bunu ayarladıktan sonra Kibana üzerinden verilerinizi arayabilir görselleştirebilirsiniz. 

#### Keyword ve Text veri tiplerini ne zaman kullanmalıyım?
 > Keyword ve Text veri tiplerini text kayıt ederken kullanırız ancak eğer keyword tipini kullanırsak veri tek bir term olarak kaydedilecektir. Mesela email gibi aramalarda da tam karşılığını arayacağımız bilgileri keyword tipinde tutabiliriz. Ancak üzerinde analiz uygulayıp aramaları analiz sonuçlarına göre yapacağımız alanları ise text tipinde tutabiliriz. 

#### Bir cluster'dan diğerine verileri nasıl taşırım?
 > ES 6.x sürümünde bunu `_reindex` [API'ı](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-reindex.html) ile rahatlıkla yapabilirsiniz. Eğer eski cluster çalışmıyor durumda (veri bozukluğu olmadığı durumlarda) ise ES'in data klasörünü yeni cluster'a taşıyarak'da yapabilirsiniz. Ancak burada her bir node'un data klasörünü yeni cluster içerisinde bir bir node'a taşımalısınız. Bu biraz daha karmaşıktır ve eski ES'de bozuk bir veri yapısı var ise yenisinde de aynı sorun oluşacaktır. 

#### RDMS'de kullandığımız trigger yapılarına benzer yapılar mevcut mu?
 > Evet mevcut. X-Pack eklentisinin Watcher ürünü üzerinden benzer bir yapı kurabiliyorsunuz. Şu aralar sanırım X-Pack'de ücretsiz oldu. Hatta [trigger](https://www.elastic.co/guide/en/watcher/current/trigger.html) yapısı mevcut ve bununla birlikte zaman tabanlı trigger oluşturabiliyorsunuz. 

#### Verileri kaybetmeden index mapping'i nasıl değiştirebilirim. 
 > Bunun için öncelikli olarak yeni mapping'de index'inizi oluşturun. Daha sonra `_reindex` API'ı kullanarak eski index'den yenisine verilerinizi taşıyabilirsiniz. Ancak veri yapınızda veri türü ya da yeni eklenen bir alan varsa ve bir hesaplama gerekiyorsa burada `_reindex` API size script yazabilme imkanı sunuyor. Eski veri üzerinde bir takım değişiklikler yaparak yeni index'e atabiliyorsunuz. 

#### Verimizi ES üzerinde nasıl organize etmeliyiz?
 > Aslında bu soru her zaman "index nedir?"den başlıyor. Index benzer dokümanları bir arada tuttuğumuz veri merkezleri olarak düşünülebilir. Burada benzer dokümanlar kavramı önemli. Veri yapısı olarak farklı dokümanları aynı index üzerinde tutmaya kalktığınızda ES daha derinlerde, gereksiz farklı türde bir sürü veri kalabalığından dolayı, yavaşlama yaşayabiliyor. Şimdi bu benzer döküman kavramı nedir? Mesela kullanıcılar üzerinde text tabanlı arama yapmak istiyorsunuz. Aynı zamanda kendi domain yapınıza göre uygulamalar tablonuz var ve bunda da text araması yapmak istiyorsunuz. Bunları ayrı index'ler üzerinde tutmanız mantıklıdır. Ancak bu şu demek değil. Uygulamalar için oluşturduğunuz index içerisinde kullanıcılara ait veri bulunmayacak. Tabii ki listeleme ya da arama sonuç sayfanızda kullanıcı için ihtiyacınız olan bilgileri koyabilirsiniz. Dokümanlar en nihayetinde nested yapıda JSON objeleri olabiliyor. Ancak kullanıcı arama sayfasındaki kullanıcı objeleriniz kadar büyük objeler olmaz daha minimal nested bir yapıda orada durabilirler. Burada veri tekrar etmiş olacak iki index'tede ama hız yönünden kazanç sağlayacaktır. (Depolama alanından kaybederken hız yönünden kazanıyoruz.) Benzer olmadığı durumlarda durum biraz daha karmaşıklaşıyor. Benzer olmayan objeler tutulmaya kalkıldığında sorunlar biraz karmaşıklaşıyor. (ES'in eski sürümlerinde karşılaşmıştık şu anki stable sürümlerde "Type" kavramı direk önerilmiyor.) ID alanı olan iki obje var ve aynı index üzerinde tutuyoruz. Ancak bazı yazılımcı arkadaşlar uygulama için UUID kullanırken kullanıcılar için auto-increment ID kullanıvermişler. Bir alan ES'de keyword/string tutulurken diğer objede  bu alan integer olacak. Veriler ES derinlerden tutulurken aggregation'lar sırasında senin toplamaya çalıştığın bazı verileri integer iken bazıları string diye hata verecektir. Uzun zamandır bu hatayı almamdım belki son sürümlerinde bir çözüm bulunmuştur ancak biz zamanında script ile cast ederek kısa çözüm, daha sonrada index'leri ayırarak uzun çözüme gitmiştik. Velhasıl konu biraz uzun. Bir soru bir cevap ile cevaplanamayacak kadar.
 
#### Herhangi bir ilişkisel veritabanı yerine neden elasticsearch üzerinde full-text search yapıyoruz ? 
> İlk olarak Elasticsearch scale etme konusunda gerçekten çok kolay bir kullanım sunuyor. Verin büyüdükçe Elastic ile bu yükü yönetmen kolaylaşıyor. Bu çok büyük bir artı.

> Postgres ya da Mysql’in sunduğu fulltext search’e göre Elasticsearch’ün sunduğu daha hızlı çalışıyor. Ayrıca Sadece fulltext search ile kalmayıp ekstra özellikler de sunuyor. 

> Elasticsearch’ün sunduğu anaylze ve sorgulama özellikleri ile verileri realtime olarak arayabilyorsunuz. (Fuzzy, Stemming, Stop Word, …). Bildiğim kadarıyla bunu postgres’de sunuyor ancak kullanım kolaylığına bakıldığında Elasticsearch’de kullanım daha kolay gibi geldi bana. Ancak bu iki taraf için de birer artı. 

> Diğer bir konu da şu. RDMS verileri store etmek için gerçekten güzel bir veritabanı. Hem verileri store ederken hem de serve ederken aynı DB’yi ekstra uğraş gerektirmeden aynı teknolojiyi yönetmek anlamına geliyor. Bu da devops konusunda daha az iş çıkarıyor. Elasticsearch’ü store db olarak kullanman tavsiye edilmiyor.  Burada RDMS tarafına bir artı koyabiliriz ancak bu artı biraz seçmeli.

> Aggregation konusunda Elasticsearch daha iyi olacağını düşünüyorum. Çünkü subaggregation’ları da düşünüce milyonlarda veriyi bir kaç kez üzerinden geçerek mili saniyeler içerisinde rahatça cevap dönüyor. Bu konuda Postgres’in ne kdar güçlü olabileceğini bilmiyorum ancak Mysql’i 30M 40M veri olan tablolarda count ya da sum vs gibi sorgular yavaş yavaş sıkıntı yaratmaya başlıyor. Elasticsearch’ü insanlar terabayt’larca veri yönetmek için kullanıyor. Burada belki scalable sistemler daha iyi sonuçlar verebilir.

> Bir sistem NoSQL diğer RDMS. NoSQL veride structure olarak bir sınırın olmak zorunda değil. Diğerinde biraz zorunlu. Burada da böyle bir artı var. 

#### `minimum_should_match` ne zaman ve nasıl kullanıyoruz? Nasıl Çalışıyor?

> Konuya ilişkin bir [yazı yazmıştım.](https://medium.com/@kulekci/elasticsearchde-minimum-should-match-kullan%C4%B1m%C4%B1-4275b8f7f0b3) Bununla birlikte sorulan soru üzerine konu üzerine tekrar bir [gist](https://gist.github.com/hkulekci/2428d8938701af895ec07b7e0cf82b85) oluşturdum ve şunu söyleyebiirim. Siz bir arama yapacaksınız. Diyelim ki 10 kriter var. Bu kriterlerin hepsinin birden eşleşmesi o kadar da önemli değil en az 5 tanesi eşleşse bile sonuçlardan memnun olacağız. İşte tam bu noktada kullanıyoruz. En iyi örneğini de daha önce baktığınız ürünlere ya da dökümanlara benzer dökümanları göstermek için kullanabilirsiniz. Burada boost özelliği sayesinde çok güzel benzer dökümanlara eşleşebiliyorsunuz. Burada domain yapınızın buna uygun olması da önemli. Aksi durumda farklı bir kategorideki tamamen alakasız bir ürünü kullanıcıya öneriyor olabilirsiniz. 
