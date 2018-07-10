# Elasticsearch Sorular

 - Elasticsearch'de PDF veya TXT file indexlemek ve indexledigim PDF veya TXT dosyasinin icindeki herhangi bir kelime ile 
arama yaptigimda listelemek istiyorum bu mümkün mü?
 > Evet Mümkün. Bunun için Apache Tika tabanlı `ingest attachment processor` eklentisini kullanabilirsiniz. Eklenti uyumlu binary dosyaları Elasticssearch üzerinde aranabilir olarak bir alan üzerinde tutulmasını sağlıyor. Daha sonra bu alan üzerinde analiz işlemleri yaparak arama sonuçlarında listelenmesini sağlayabilirsiniz.
 -	Bir mysql veritabanını Elasticsearch’e nasıl atarız?
 > Mysql'den verileri taşımak için Bulk Insert methodunu kullanan bir script yazabilirsin. Bunun için herhangi bir dilde komut satırından deamon olarak çalışan bir sistem kurabilirsin. Eskiden bunun için plugin'ler vardı river diye ama yeni sürümlerde yok. Ayrıca Logstash'de odbc eklentisi ile bir mysql bağlantısı açıp sorgu ile belirli aralıklarla veri aktarımı da yapabiliyorsun.
 -	İndex’teki verilerden asıl veriye nasıl geçiş yaparız?
 > Index'teki veriler zaten asıl veriler. Inverted index senin yönetmen gereken bişey değil. Onu ES kendisi yönetiyor. Sen ayrıca bir şey yapman gerekmiyor. Sen verileri kaydederken ES bir kısmını bir inverted index'e verinin kendisini eğer sen aksi bir durum belirtmediysen dosya sistemine kaydediyor.
 -	Inverted index ve index farkı nedir? Bunlar ayrı ayrı mı oluşuyorlar? Ya da ikisi de aynı anda mı oluşuyor?
 > Index verilerin tutulduğu veritabanı gibi düşünebilirsin. Birbirine benzer veriler index'lerde tutuluyor. Inverted index ise senin hızlı arama yapabilmen için bir arama yapısı.
 -	Biz bir sorgu çalıştırdığımızda elasticsearch ilk önce inverted index’e bakıp sonra index’e mi geçiş yapıyor? Yoksa direk index’te mi arıyor? 
 >  Index verilerin tutulduğu veritabanı gibi düşünebilirsin. Birbirine benzer veriler index'lerde tutuluyor. Inverted index ise senin hızlı arama yapabilmen için bir arama yapısı.
