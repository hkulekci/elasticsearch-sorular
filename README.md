# Elasticsearch Sorular

 - Elasticsearch'de PDF veya TXT file indexlemek ve indexledigim PDF veya TXT dosyasinin icindeki herhangi bir kelime ile 
arama yaptigimda listelemek istiyorum bu mumkunmu?
 > Evet Mümkün. Bunun için Apache Tika tabanlı `ingest attachment processor` eklentisini kullanabilirsiniz. Eklenti uyumlu binary dosyaları Elasticssearch üzerinde aranabilir olarak bir alan üzerinde tutulmasını sağlıyor. Daha sonra bu alan üzerinde analiz işlemleri yaparak arama sonuçlarında listelenmesini sağlayabilirsiniz.  
