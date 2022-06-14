.. _intro-overview:

==================
Bir bakışta Scrapy
==================

Scrapy, veri madenciliği, bilgi işleme veya geçmiş arşivleme gibi çok çeşitli faydalı uygulamalar için kullanılabilen 
web sitelerini taramak ve yapılandırılmış verileri çıkarmak için bir framework çerçevesidir.

Scrapy başlangıçta `web scraping`_ için tasarlanmış olsa da , API'leri ( Amazon Associates Web Services gibi ) 
kullanarak veya genel amaçlı bir web tarayıcısı olarak veri çıkarmak için de kullanılabilir .


Spider Örneğini Gözden Geçirelim
=================================

Scrapy'nin masaya ne getirdiğini göstermek için, 
bir spider çalıştırmak için en basit yolu kullanarak bir Scrapy Spider örneğinden geçeceğiz.

Here's the code for a spider that scrapes famous quotes from website
http://quotes.toscrape.com, following the pagination::

    import scrapy


    class QuotesSpider(scrapy.Spider):
        name = 'quotes'
        start_urls = [
            'http://quotes.toscrape.com/tag/humor/',
        ]

        def parse(self, response):
            for quote in response.css('div.quote'):
                yield {
                    'author': quote.xpath('span/small/text()').get(),
                    'text': quote.css('span.text::text').get(),
                }

            next_page = response.css('li.next a::attr("href")').get()
            if next_page is not None:
                yield response.follow(next_page, self.parse)

Bunu bir metin dosyasına koyun, buna benzer bir ad verin 
quotes_spider.py ve şu :command:`runspider` command:: komutu kullanarak Spider’ı çalıştırın:

    scrapy runspider quotes_spider.py -o quotes.jl

Bu bittiğinde, quotes.jl dosyada metin ve yazar içeren JSON Lines 
formatında aşağıdaki gibi görünen alıntıların bir listesi olacaktır::

    {"author": "Jane Austen", "text": "\u201cThe person, be it gentleman or lady, who has not pleasure in a good novel, must be intolerably stupid.\u201d"}
    {"author": "Steve Martin", "text": "\u201cA day without sunshine is like, you know, night.\u201d"}
    {"author": "Garrison Keillor", "text": "\u201cAnyone who thinks sitting in church can make you a Christian must also think that sitting in a garage can make you a car.\u201d"}
    ...


Az Önce Ne Yaptık?
-------------------

Komutu çalıştırdığınızda, Scrapy içinde bir Spider tanımı aradı 
ve onu tarayıcı motorunda çalıştırdı. Scrapy runspider quotes_spider.py

Tarama, start_urls öznitelikte tanımlanan URL'lere (bu durumda, yalnızca humor kategorisindeki tırnakların URL'si) isteklerde bulunarak başladı parse ve yanıt nesnesini bir argüman olarak ileterek varsayılan geri arama yöntemini çağırdı. Parse Geri aramada, bir CSS Seçici kullanarak alıntı öğeleri arasında dolaşıyoruz, ayıklanan alıntı metni ve yazar ile bir Python diktisi oluşturuyoruz, sonraki sayfaya bir bağlantı arıyoruz ve geri arama ile aynı parse yöntemi kullanarak başka bir istek planlıyoruz.

Burada, Scrapy'nin ana avantajlarından birini fark edeceksiniz: istekler zaman uyumsuz olarak planlanır ve işlenir. Bu, Scrapy'nin bir isteğin tamamlanmasını ve işlenmesini beklemesine gerek olmadığı, bu arada başka bir istek gönderebileceği veya başka şeyler yapabileceği anlamına gelir. Bu ayrıca, bazı istekler başarısız olsa veya işlenirken bir hata meydana gelse bile diğer isteklerin devam edebileceği anlamına gelir.

Bu, çok hızlı taramalar yapmanızı sağlarken (aynı anda birden fazla eşzamanlı istek gönderme, hataya dayanıklı bir şekilde) Scrapy ayrıca birkaç ayar aracılığıyla taramanın nezaketi üzerinde kontrol sahibi olmanızı sağlar. Her istek arasında bir indirme gecikmesi ayarlamak, etki alanı veya IP başına eşzamanlı istek miktarını sınırlamak ve hatta bunları otomatik olarak bulmaya çalışan bir otomatik kısıtlama uzantısı kullanmak gibi şeyler yapabilirsiniz.

.. note::

    Bu, JSON dosyasını oluşturmak için feed dışa aktarmalarını kullanır, dışa aktarma biçimini (örneğin XML veya CSV) veya depolama arka ucunu (örneğin FTP veya Amazon S3) kolayca değiştirebilirsiniz. Öğeleri bir veritabınında depolamak için bir öğe ardışık düzen de yazabilirsiniz


.. _topics-whatelse:

Başka Neler var?
==========

Scrapy kullanarak bir web sitesinden öğelerin nasıl çıkarılacağını ve saklanacağını gördünüz, ancak bu sadece yüzeyi. Scrapy, kazımayı kolay ve verimli hale getirmek için birçok güçlü özellik sunar, örneğin:

* Normal ifadeleri kullanarak ayıklamak için yardımcı yöntemlerle, genişletilmiş CSS seçicileri ve XPath ifadeleri kullanarak HTML/XML kaynaklarından veri seçme ve ayıklama için yerleşik destek sunar.

* Verileri sıyırmak için CSS ve XPath ifadelerini denemek için etkileşimli bir kabuk konsolu, spider’ları yazarken veya hatalarını ayıklarken çok kullanışlıdır. 

* Birden çok biçimde besleme dışa aktarmaları oluşturmak (JSON, CSV, XML) ve bunları birden çok arka uçta depolamak için yerleşik destek (FTP, S3, local filesystem)

* Yabancı, standart dışı ve bozuk kodlama bildirimleriyle başa çıkmak için güçlü kodlama desteği ve otomatik algılama.

* Güçlü genişletilebilirlik desteği ve iyi tanımlanmış bir API'yi (Middleware, extensions ve pipelines) kullanarak kendi işlevselliğinizi eklemenize olanak tanır.

* Kullanım için çok çeşitli yerleşik uzantılar ve ara yazılımlar:

  - Çerezler ve oturum yönetimi
  - Sıkıştırma, kimlik doğrulama, önbelleğe alma gibi HTTP özellikleri
  - Kullanıcı aracısı sahtekarlığı
  - robots.txt
  - Tarama derinliği kısıtlaması

  - Ve dahası

Sırada Ne Var?
============

Sizin için sonraki adımlar, Scrapy'yi kurmak, tam gelişmiş bir Scrapy projesinin nasıl oluşturulacağını öğrenmek için öğreticiyi takip etmeye devam edin. İlginiz İçin Teşekkürler!

.. _join the community: https://scrapy.org/community/
.. _web scraping: https://en.wikipedia.org/wiki/Web_scraping
.. _Amazon Associates Web Services: https://affiliate-program.amazon.com/gp/advertising/api/detail/main.html
.. _Amazon S3: https://aws.amazon.com/s3/
.. _Sitemaps: https://www.sitemaps.org/index.html
