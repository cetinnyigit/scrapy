.. _intro-tutorial:

===============
Scrapy Eğitimi
===============

Bu öğreticide, Scrapy'nin sisteminize zaten yüklendiğini varsayacağız. Durum böyle değilse, bkz. Kurulum kılavuzu.

Ünlü yazarların alıntılarını listeleyen bir web sitesi olan quotes.toscrape.com kazıyacağız.

Bu eğitim size şu görevlerde yardımcı olacaktır:

1. Yeni bir Scrapy projesi oluşturma
2. Bir siteyi taramak ve veri çıkarmak için bir spider yazma
3. Komut satırını kullanarak kazınmış verileri dışa aktarma
4. spider'ı tekrarlayan bağlantıları takip etmek için değiştirme
5. Spider argümanlarını kullanma

Scrapy Python'da yazılmıştır. Dilde yeniyseniz, Scrapy'den en iyi şekilde yararlanmak için dilin nasıl olduğu hakkında bir fikir edinerek başlamak isteyebilirsiniz.

Zaten diğer dillere aşinaysanız ve Python'u hızlı bir şekilde öğrenmek istiyorsanız, Python Öğreticisi iyi bir kaynaktır.

Programlamada yeniyseniz ve Python ile başlamak istiyorsanız, aşağıdaki kitaplar
sizin için yararlı olabilir:

* `Automate the Boring Stuff With Python`_

* `How To Think Like a Computer Scientist`_

* `Learn Python 3 The Hard Way`_

Programcı olmayanlar için bu Python kaynakları listesine ve learnpython-subredditte önerilen kaynaklara da göz atabilirsiniz.

.. _Python: https://www.python.org/
.. _this list of Python resources for non-programmers: https://wiki.python.org/moin/BeginnersGuide/NonProgrammers
.. _Python Tutorial: https://docs.python.org/3/tutorial
.. _Automate the Boring Stuff With Python: https://automatetheboringstuff.com/
.. _How To Think Like a Computer Scientist: http://openbookproject.net/thinkcs/python/english3e/
.. _Learn Python 3 The Hard Way: https://learnpythonthehardway.org/python3/
.. _suggested resources in the learnpython-subreddit: https://www.reddit.com/r/learnpython/wiki/index#wiki_new_to_python.3F


Proje oluşturma
==================

Kazımaya başlamadan önce yeni bir Scrapy projesi kurmanız gerekecek. Girin
kodunuzu saklamak ve çalıştırmak istediğiniz dizin::

    scrapy startproject tutorial

Bu, aşağıdaki içeriği içeren bir eğitim dizini oluşturur::

    tutorial/
        scrapy.cfg            # deploy configuration file

        tutorial/             # project's Python module, you'll import your code from here
            __init__.py

            items.py          # project items definition file

            middlewares.py    # project middlewares file

            pipelines.py      # project pipelines file

            settings.py       # project settings file

            spiders/          # a directory where you'll later put your spiders
                __init__.py


İlk Spider'ımız
================

Spiderlar, tanımladığınız ve Scrapy'nin bir web sitesinden (veya bir grup web sitesinden) bilgi kazımak için kullandığı sınıflardır. Spider sınıfını alt sınıflandırmalı ve yapılacak ilk istekleri, isteğe bağlı olarak sayfalardaki bağlantıları nasıl takip edeceklerini ve veri çıkarmak için indirilen sayfa içeriğinin nasıl ayrıştırılacağını tanımlamalıdırlar.

Bu ilk Spider'ımızın kodu. Projenizdeki Tutorial/Spiders dizininin altındaki quotes_spider.py adlı bir dosyaya kaydedin::

    import scrapy


    class QuotesSpider(scrapy.Spider):
        name = "quotes"

        def start_requests(self):
            urls = [
                'http://quotes.toscrape.com/page/1/',
                'http://quotes.toscrape.com/page/2/',
            ]
            for url in urls:
                yield scrapy.Request(url=url, callback=self.parse)

        def parse(self, response):
            page = response.url.split("/")[-2]
            filename = f'quotes-{page}.html'
            with open(filename, 'wb') as f:
                f.write(response.body)
            self.log(f'Saved file {filename}')


Gördüğünüz gibi, Spider alt sınıfı Scrapy. Spider ve bazı nitelikleri ve yöntemleri tanımlar:

* :attr:`~scrapy.Spider.name`: Spider'ı tanımlar. Bir proje içinde benzersiz olmalı, yani farklı Spider'lar için aynı adı ayarlayamazsınız.

* :meth:`~scrapy.Spider.start_requests`: Spider'ın taramaya başlayacağı İsteklerin yinelenebilir bir özelliğini iade etmelidir (isteklerin bir listesini döndürebilir veya bir jeneratör işlevi yazabilirsiniz). Sonraki istekler bu ilk taleplerden art arda oluşturulacaktır.

* :meth:`~scrapy.Spider.parse`: yapılan isteklerin her biri için indirilen yanıtı işlemek üzere çağrılacak bir yöntem. Yanıt parametresi, sayfa içeriğini tutan ve sayfayı işlemek için daha yararlı yöntemlere sahip bir Text Response örneğidir.

parse () yöntemi genellikle yanıtı ayrıştırır, kazınmış verileri dikte olarak çıkarır ve ayrıca takip edilecek yeni URL'ler bulur ve onlardan yeni istekler (İstek) oluşturur.

Spider'ımızı nasıl çalıştırırız
---------------------

Spider'ımızı işe koymak için projenin en üst düzey dizinine gidin ve çalıştırın::

   scrapy crawl quotes

Bu komut, Spider'ı az önce eklediğimiz ve quotes.toscrape.com alan adı için bazı 
istekler gönderecek isim alıntılarıyla çalıştırır. Buna benzer bir çıktı alacaksınız::

    ... (omitted for brevity)
    2016-12-16 21:24:05 [scrapy.core.engine] INFO: Spider opened
    2016-12-16 21:24:05 [scrapy.extensions.logstats] INFO: Crawled 0 pages (at 0 pages/min), scraped 0 items (at 0 items/min)
    2016-12-16 21:24:05 [scrapy.extensions.telnet] DEBUG: Telnet console listening on 127.0.0.1:6023
    2016-12-16 21:24:05 [scrapy.core.engine] DEBUG: Crawled (404) <GET http://quotes.toscrape.com/robots.txt> (referer: None)
    2016-12-16 21:24:05 [scrapy.core.engine] DEBUG: Crawled (200) <GET http://quotes.toscrape.com/page/1/> (referer: None)
    2016-12-16 21:24:05 [scrapy.core.engine] DEBUG: Crawled (200) <GET http://quotes.toscrape.com/page/2/> (referer: None)
    2016-12-16 21:24:05 [quotes] DEBUG: Saved file quotes-1.html
    2016-12-16 21:24:05 [quotes] DEBUG: Saved file quotes-2.html
    2016-12-16 21:24:05 [scrapy.core.engine] INFO: Closing spider (finished)
    ...

Şimdi, geçerli dizindeki dosyaları kontrol edin. Ayrıştırma yöntemimizin talimatlarına göre, 
iki yeni dosya oluşturulduğunu fark etmelisiniz: alıntılar-1.html ve alıntılar-2.html, ilgili URL'lerin içeriğiyle.

.. note:: HTML'yi neden henüz ayrıştırmadığımızı merak ediyorsanız, bekleyin, yakında ele alacağız.


Kaputun altında ne oldu?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Scrapy programlar. Spider'ın start_requests yöntemiyle iade edilen nesneleri isteyin. Her biri için bir yanıt aldıktan sonra, Yanıt nesnelerini başlatır ve yanıtı argüman olarak ileten istekle (bu durumda ayrıştırma yöntemi) ilişkili geri çağırma yöntemini çağırır.


start_requests yönteminin kısayolu
---------------------------------------
Scrapy oluşturan bir start_requests () yöntemi uygulamak yerine. URL'lerden nesne isteyin, sadece URL'lerin listesiyle bir start_urls sınıfı niteliği tanımlayabilirsiniz. Bu liste daha sonra Spider'ımızın için ilk istekleri oluşturmak üzere varsayılan start_requests () uygulamasıyla kullanılacaktır::

    import scrapy


    class QuotesSpider(scrapy.Spider):
        name = "quotes"
        start_urls = [
            'http://quotes.toscrape.com/page/1/',
            'http://quotes.toscrape.com/page/2/',
        ]

        def parse(self, response):
            page = response.url.split("/")[-2]
            filename = f'quotes-{page}.html'
            with open(filename, 'wb') as f:
                f.write(response.body)

Parse () yöntemi, Scrapy'ye bunu açıkça söylememiş olsak da, bu URL'lerin her bir talebini karşılamak için çağrılacaktır. Bu, ayrıştırma (), Scrapy'nin açıkça atanmış bir geri arama olmadan istekler için çağrılan varsayılan geri arama yöntemidir.


Veri çıkarma
---------------

Scrapy ile veri çıkarmayı öğrenmenin en iyi yolu, Scrapy Shell kullanarak seçicileri denemektir. Çalıştır::

    scrapy shell 'http://quotes.toscrape.com/page/1/'

.. note::

   Scrapy kabuğunu komut satırından çalıştırırken url'leri her zaman tırnak içinde kapatmayı unutmayın, aksi takdirde argümanlar içeren urls (yani karakter) çalışmaz.

Windows'ta bunun yerine çift tırnak kullanın::

       scrapy shell "http://quotes.toscrape.com/page/1/"

Şöyle bir şey göreceksiniz::

    [ ... Scrapy log here ... ]
    2016-09-19 12:09:27 [scrapy.core.engine] DEBUG: Crawled (200) <GET http://quotes.toscrape.com/page/1/> (referer: None)
    [s] Available Scrapy objects:
    [s]   scrapy     scrapy module (contains scrapy.Request, scrapy.Selector, etc)
    [s]   crawler    <scrapy.crawler.Crawler object at 0x7fa91d888c90>
    [s]   item       {}
    [s]   request    <GET http://quotes.toscrape.com/page/1/>
    [s]   response   <200 http://quotes.toscrape.com/page/1/>
    [s]   settings   <scrapy.settings.Settings object at 0x7fa91d888c10>
    [s]   spider     <DefaultSpider 'default' at 0x7fa91c8af990>
    [s] Useful shortcuts:
    [s]   shelp()           Shell help (print this help)
    [s]   fetch(req_or_url) Fetch request (or URL) and update local objects
    [s]   view(response)    View response in a browser

Kabuğu kullanarak, yanıt nesnesiyle CSS kullanarak öğeleri seçmeyi deneyebilirsiniz::

.. invisible-code-block: python

    response = load_response('http://quotes.toscrape.com/page/1/', 'quotes1.html')

>>> response.css('title')
[<Selector xpath='descendant-or-self::title' data='<title>Quotes to Scrape</title>'>]

Response.css ('başlık') çalıştırmanın sonucu, XML/HTML öğelerinin etrafını saran ve seçimi ince taneli hale getirmek veya verileri ayıklamak için daha fazla sorgu çalıştırmanıza izin veren Selector List adlı liste benzeri bir nesnedir.

Metni yukarıdaki başlıktan çıkarmak için şunları yapabilirsiniz::

>>> response.css('title::text').getall()
['Quotes to Scrape']

Burada dikkat edilmesi gereken iki şey vardır: birincisi ekledik: CSS sorgusuna metin, yani yalnızca < başlık > öğenin içindeki metin öğelerini doğrudan seçmek istiyoruz. Belirtmezsek: metin, etiketleri de dahil olmak üzere başlık öğesinin tamamını alırız::

>>> response.css('title').getall()
['<title>Quotes to Scrape</title>']

Diğer bir şey, .getall () çağrısının sonucunun bir liste olmasıdır: bir seçicinin birden fazla sonuç döndürmesi mümkündür, bu yüzden hepsini çıkarırız. Sadece ilk sonucu istediğinizi bildiğinizde, bu durumda olduğu gibi, şunları yapabilirsiniz::

>>> response.css('title::text').get()
'Quotes to Scrape'

Alternatif olarak şöyle yazabilirdiniz::

>>> response.css('title::text')[0].get()
'Quotes to Scrape'

Bir SelectorList örneğinde bir dizine erişmek, sonuç yoksa Index Error istisnasını yükseltir::

    >>> response.css('noelement')[0].get()
    Traceback (most recent call last):
    ...
    IndexError: list index out of range

Bunun yerine doğrudan SelectorList örneğinde .get () kullanmak isteyebilirsiniz, bu da sonuç yoksa Yok'u döndürür::

>>> response.css("noelement").get()

Burada bir ders var: çoğu kazıma kodu için, bir sayfada bulunmayan şeyler nedeniyle hatalara dayanıklı olmasını istersiniz, böylece bazı parçalar kazınmasa bile en azından bazı veriler alabilirsiniz.

getall () ve get () yöntemlerinin yanı sıra, normal ifadeleri kullanarak ayıklamak için re () yöntemini de kullanabilirsiniz::

>>> response.css('title::text').re(r'Quotes.*')
['Quotes to Scrape']
>>> response.css('title::text').re(r'Q\w+')
['Quotes']
>>> response.css('title::text').re(r'(\w+) to (\w+)')
['Quotes', 'Scrape']

Kullanılacak uygun CSS seçicilerini bulmak için, yanıt sayfasını web tarayıcınızdaki kabuktan görünüm (yanıt) kullanarak açarken kullanışlı bulabilirsiniz. HTML'yi incelemek ve bir seçici bulmak için tarayıcınızın geliştirici araçlarını kullanabilirsiniz (bkz. Tarayıcınızın kazıma için Geliştirici Araçlarını Kullanma).

Seçici Gadget, birçok tarayıcıda çalışan görsel olarak seçilmiş öğeler için CSS seçicisini hızlı bir şekilde bulmak için güzel bir araçtır.

.. _Selector Gadget: https://selectorgadget.com/


XPath: kısa bir giriş
^^^^^^^^^^^^^^^^^^^^

CSS'nin yanı sıra, Scrapy seçiciler de XPath ifadelerini kullanmayı destekler::

>>> response.xpath('//title')
[<Selector xpath='//title' data='<title>Quotes to Scrape</title>'>]
>>> response.xpath('//title/text()').get()
'Quotes to Scrape'

XPath ifadeleri çok güçlüdür ve Scrapy Selectors'ın temelidir. Aslında, CSS seçicileri kaputun altındaki XPath'a dönüştürülür. Kabuktaki seçici nesnelerin metin temsilini yakından okursanız görebilirsiniz.

Belki de CSS seçicileri kadar popüler olmasa da, XPath ifadeleri daha fazla güç sunar, çünkü yapıda gezinmenin yanı sıra içeriğe de bakabilir. XPath kullanarak şu şeyleri seçebilirsiniz: "Sonraki Sayfa" metnini içeren bağlantıyı seçin. Bu, XPath'ı kazıma görevine çok uygun hale getirir ve CSS seçicilerinin nasıl oluşturulacağını zaten biliyor olsanız bile, kazıma işlemini çok daha kolay hale getirecektir.

Burada XPath'ın çoğunu kapsamayacağız, ancak Scrapy Selectors ile XPath kullanma hakkında daha fazla bilgiyi buradan okuyabilirsiniz. XPath hakkında daha fazla bilgi edinmek için, bu öğreticinin XPath'ı örnekler aracılığıyla öğrenmesini ve bu öğreticinin "XPath'da nasıl düşünüleceğini" öğrenmesini öneririz.


.. _XPath: https://www.w3.org/TR/xpath/all/
.. _CSS: https://www.w3.org/TR/selectors

Alıntılar ve yazarlar çıkarılıyor
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Artık seçim ve çıkarma hakkında biraz bilgi sahibi olduğunuza göre, web sayfasından alıntıları çıkarmak için kodu yazarak örümceğimizi tamamlayalım.

https://quotes.toscrape.com her alıntı şu şekilde görünen HTML öğeleriyle temsil edilir::

.. code-block:: html

    <div class="quote">
        <span class="text">“The world as we have created it is a process of our
        thinking. It cannot be changed without changing our thinking.”</span>
        <span>
            by <small class="author">Albert Einstein</small>
            <a href="/author/Albert-Einstein">(about)</a>
        </span>
        <div class="tags">
            Tags:
            <a class="tag" href="/tag/change/page/1/">change</a>
            <a class="tag" href="/tag/deep-thoughts/page/1/">deep-thoughts</a>
            <a class="tag" href="/tag/thinking/page/1/">thinking</a>
            <a class="tag" href="/tag/world/page/1/">world</a>
        </div>
    </div>

İstediğimiz verileri nasıl çıkaracağımızı öğrenmek için sıyrık kabuğu açalım ve biraz oynayalım::

    $ scrapy shell 'http://quotes.toscrape.com'

Alıntı HTML öğeleri için seçicilerin bir listesini aşağıdakilerle alırız::

>>> response.css("div.quote")
[<Selector xpath="descendant-or-self::div[@class and contains(concat(' ', normalize-space(@class), ' '), ' quote ')]" data='<div class="quote" itemscope itemtype...'>,
 <Selector xpath="descendant-or-self::div[@class and contains(concat(' ', normalize-space(@class), ' '), ' quote ')]" data='<div class="quote" itemscope itemtype...'>,
 ...]

Yukarıdaki sorgu tarafından döndürülen seçicilerin her biri, alt öğeleri üzerinde daha fazla sorgu yapmamıza izin verir. İlk seçiciyi bir değişkene atayalım, böylece CSS seçicilerimizi doğrudan belirli bir alıntıda çalıştırabiliriz:

>>> quote = response.css("div.quote")[0]

Şimdi, yeni oluşturduğumuz alıntı nesnesini kullanarak bu alıntıdan metin, yazar ve etiketleri çıkaralım::

>>> text = quote.css("span.text::text").get()
>>> text
'“The world as we have created it is a process of our thinking. It cannot be changed without changing our thinking.”'
>>> author = quote.css("small.author::text").get()
>>> author
'Albert Einstein'

Etiketlerin dizelerin bir listesi olduğu göz önüne alındığında, hepsini almak için .getall () yöntemini kullanabiliriz::

>>> tags = quote.css("div.tags a.tag::text").getall()
>>> tags
['change', 'deep-thoughts', 'thinking', 'world']

.. invisible-code-block: python

  from sys import version_info

Her biti nasıl çıkaracağımızı anladıktan sonra, artık tüm alıntı öğelerini yineleyebilir ve bir Python sözlüğünde bir araya getirebiliriz::

>>> for quote in response.css("div.quote"):
...     text = quote.css("span.text::text").get()
...     author = quote.css("small.author::text").get()
...     tags = quote.css("div.tags a.tag::text").getall()
...     print(dict(text=text, author=author, tags=tags))
{'text': '“The world as we have created it is a process of our thinking. It cannot be changed without changing our thinking.”', 'author': 'Albert Einstein', 'tags': ['change', 'deep-thoughts', 'thinking', 'world']}
{'text': '“It is our choices, Harry, that show what we truly are, far more than our abilities.”', 'author': 'J.K. Rowling', 'tags': ['abilities', 'choices']}
...

Spider'ımızdaki verilerin çıkarılması
-----------------------------

Spider'ımıza dönelim. Şimdiye kadar, özellikle herhangi bir veri çıkarmaz, sadece tüm HTML sayfasını yerel bir dosyaya kaydeder. Yukarıdaki çıkarma mantığını örümceğimize entegre edelim.

Bir Scrapy Spider'ı tipik olarak sayfadan çıkarılan verileri içeren birçok sözlük üretir. Bunu yapmak için, aşağıda görebileceğiniz gibi, geri çağrıda Python anahtar kelimesini kullanıyoruz::

    import scrapy


    class QuotesSpider(scrapy.Spider):
        name = "quotes"
        start_urls = [
            'http://quotes.toscrape.com/page/1/',
            'http://quotes.toscrape.com/page/2/',
        ]

        def parse(self, response):
            for quote in response.css('div.quote'):
                yield {
                    'text': quote.css('span.text::text').get(),
                    'author': quote.css('small.author::text').get(),
                    'tags': quote.css('div.tags a.tag::text').getall(),
                }
Bu Spider'ı çalıştırırsanız, çıkarılan verileri günlükle birlikte çıkarır::

    2016-09-19 18:57:19 [scrapy.core.scraper] DEBUG: Scraped from <200 http://quotes.toscrape.com/page/1/>
    {'tags': ['life', 'love'], 'author': 'André Gide', 'text': '“It is better to be hated for what you are than to be loved for what you are not.”'}
    2016-09-19 18:57:19 [scrapy.core.scraper] DEBUG: Scraped from <200 http://quotes.toscrape.com/page/1/>
    {'tags': ['edison', 'failure', 'inspirational', 'paraphrased'], 'author': 'Thomas A. Edison', 'text': "“I have not failed. I've just found 10,000 ways that won't work.”"}


.. _storing-data:

Kazınan verilerin depolanması
========================

Kazınan verileri saklamanın en basit yolu, aşağıdaki komutla Feed dışa aktarımlarını kullanmaktır::

    scrapy crawl quotes -O quotes.json

Bu, Json da serileştirilmiş tüm kazınmış öğeleri içeren bir quotes.json dosyası oluşturur.

-O komut satırı anahtarı varolan tüm dosyaların üzerine yazar; varolan herhangi bir dosyaya yeni içerik eklemek için -o kullanın. Ancak, bir JSON dosyasına eklenmesi dosya içeriğini geçersiz kılar. Bir dosyaya eklenirken, JSON Lines gibi farklı bir serileştirme formatı kullanmayı düşünün::

    scrapy crawl quotes -o quotes.jl
JSON Lines formatı kullanışlıdır, çünkü akışa benzer, yeni kayıtları kolayca ekleyebilirsiniz. İki kez koştuğunuzda aynı JSON sorunu yoktur. Ayrıca, her kayıt ayrı bir satır olduğundan, büyük dosyaları belleğe her şeyi sığdırmak zorunda kalmadan işleyebilirsiniz, komut satırında bunu yapmaya yardımcı olacak JQ gibi araçlar vardır.

Küçük projelerde (bu öğreticideki gibi), bu yeterli olmalıdır. Ancak, kazınmış öğelerle daha karmaşık şeyler yapmak istiyorsanız, bir Öğe Boru Hattı yazabilirsiniz. Proje oluşturulduğunda Öğe Boru Hatları için bir yer tutucu dosyası, öğretici/pipelines.py adresinde ayarlanmıştır. Kazınmış eşyaları saklamak istiyorsanız, herhangi bir öğe boru hattı uygulamanıza gerek yoktur.

.. _JSON Lines: http://jsonlines.org
.. _JQ: https://stedolan.github.io/jq


Aşağıdaki bağlantılar
===============

Diyelim ki, https://quotes.toscrape.com ilk iki sayfasındaki şeyleri kazımak yerine, web sitesindeki tüm sayfalardan alıntılar istiyorsunuz.

Artık sayfalardan nasıl veri çıkaracağınızı bildiğinize göre, onlardan bağlantıları nasıl takip edeceğinize bakalım.

İlk iş, takip etmek istediğimiz sayfanın bağlantısını çıkarmak. Sayfamızı incelerken, aşağıdaki işaretlemeyi içeren bir sonraki sayfaya bağlantı olduğunu görebiliriz::

.. code-block:: html

    <ul class="pager">
        <li class="next">
            <a href="/page/2/">Next <span aria-hidden="true">&rarr;</span></a>
        </li>
    </ul>

Shell'den çıkarmayı deneyebiliriz.

>>> response.css('li.next a').get()
'<a href="/page/2/">Next <span aria-hidden="true">→</span></a>'

Bu, çapa öğesini alır, ancak href niteliğini istiyoruz. Bunun için Scrapy, nitelik içeriğini seçmenize olanak tanıyan bir CSS uzantısını destekler::

>>> response.css('li.next a::attr(href)').get()
'/page/2/'

Ayrıca bir özellik de mevcuttur (bkz. Daha fazlası için öğe niteliklerini seçme)::

>>> response.css('li.next a').attrib['href']
'/page/2/'

Şimdi Spider'ımızın bir sonraki sayfaya olan bağlantıyı tekrarlayan bir şekilde takip etmek için değiştirildiğini ve ondan veri çıkardığını görelim::

    import scrapy


    class QuotesSpider(scrapy.Spider):
        name = "quotes"
        start_urls = [
            'http://quotes.toscrape.com/page/1/',
        ]

        def parse(self, response):
            for quote in response.css('div.quote'):
                yield {
                    'text': quote.css('span.text::text').get(),
                    'author': quote.css('small.author::text').get(),
                    'tags': quote.css('div.tags a.tag::text').getall(),
                }

            next_page = response.css('li.next a::attr(href)').get()
            if next_page is not None:
                next_page = response.urljoin(next_page)
                yield scrapy.Request(next_page, callback=self.parse)


Şimdi, verileri çıkardıktan sonra, parse () yöntemi bir sonraki sayfaya bağlantıyı arar, urljoin kullanarak tam bir mutlak URL oluşturur() yöntemi (bağlar göreceli olabileceğinden) ve bir sonraki sayfaya yeni bir istek getirerek, bir sonraki sayfanın veri çıkarma işlemini işlemek ve taramayı tüm sayfalarda sürdürmek için kendisini geri arama olarak kaydeder.

Burada gördüğünüz şey Scrapy'nin bağları takip etme mekanizmasıdır: Geri arama yönteminde bir İstek verdiğinizde, Scrapy bu isteğin gönderilmesini planlar ve bu istek bittiğinde yürütülecek bir geri arama yöntemini kaydeder.

Bunu kullanarak, tanımladığınız kurallara göre bağlantıları takip eden karmaşık tarayıcılar oluşturabilir ve ziyaret ettiği sayfaya bağlı olarak farklı türde veriler çıkarabilirsiniz.

Örneğimizde, blogları, forumları ve pajinasyonu olan diğer siteleri taramak için kullanışlı olana kadar bir sonraki sayfaya olan tüm bağlantıları takip ederek bir tür döngü oluşturur.

.. _response-follow-example:

İstekler Oluşturma Kısayolu
--------------------------------

İstek nesneleri oluşturmanın kısayolu olarak response.follow öğesini kullanabilirsiniz::

    import scrapy


    class QuotesSpider(scrapy.Spider):
        name = "quotes"
        start_urls = [
            'http://quotes.toscrape.com/page/1/',
        ]

        def parse(self, response):
            for quote in response.css('div.quote'):
                yield {
                    'text': quote.css('span.text::text').get(),
                    'author': quote.css('span small::text').get(),
                    'tags': quote.css('div.tags a.tag::text').getall(),
                }

            next_page = response.css('li.next a::attr(href)').get()
            if next_page is not None:
                yield response.follow(next_page, callback=self.parse)

UScrapy'nin aksine. Request, response.follow doğrudan göreceli URL'leri destekler - urljoin çağırmanıza gerek yoktur. response.follow'un sadece bir İstek örneği gönderdiğini unutmayın; Hala bu İsteği vermek zorundasınız.

Ayrıca bir seçiciyi bir dize yerine response.follow adresine iletebilirsiniz; bu seçici gerekli nitelikleri ayıklamalıdır:

    for href in response.css('ul.pager a::attr(href)'):
        yield response.follow(href, callback=self.parse)

< a > öğeler için bir kısayol vardır: response.follow href niteliklerini otomatik olarak kullanır. Böylece kod daha da kısaltılabilir:

    for a in response.css('ul.pager a'):
        yield response.follow(a, callback=self.parse)

Yinelenebilir bir istekten birden fazla istek oluşturmak için bunun yerine response.follow_all kullanabilirsiniz:

    anchors = response.css('ul.pager a')
    yield from response.follow_all(anchors, callback=self.parse)

veya daha da kısaltmak:

    yield from response.follow_all(css='ul.pager a', callback=self.parse)


Daha fazla örnek ve desen
--------------------------

İşte bu kez yazar bilgilerini kazımak için geri aramaları ve aşağıdaki bağlantıları gösteren başka bir örümcek::

    import scrapy


    class AuthorSpider(scrapy.Spider):
        name = 'author'

        start_urls = ['http://quotes.toscrape.com/']

        def parse(self, response):
            author_page_links = response.css('.author + a')
            yield from response.follow_all(author_page_links, self.parse_author)

            pagination_links = response.css('li.next a')
            yield from response.follow_all(pagination_links, self.parse)

        def parse_author(self, response):
            def extract_with_css(query):
                return response.css(query).get(default='').strip()

            yield {
                'name': extract_with_css('h3.author-title::text'),
                'birthdate': extract_with_css('.author-born-date::text'),
                'bio': extract_with_css('.author-description::text'),
            }

Bu örümcek ana sayfadan başlayacak, her biri için parse_author geri aramayı çağıran yazar sayfalarına tüm bağlantıları ve daha önce gördüğümüz gibi ayrıştırma geri çağrısı ile pajinasyon bağlantılarını takip edecektir.

Burada kodu kısaltmak için konumsal argümanlar olarak response.follow_all geri çağrıları iletiyoruz; İstek için de çalışır.

parse_author geri arama, bir CSS sorgusundan verileri ayıklamak ve temizlemek için bir yardımcı işlevi tanımlar ve yazar verileriyle Python dictini verir.

Bu örümceğin gösterdiği bir diğer ilginç şey, aynı yazardan çok sayıda alıntı olsa bile, aynı yazar sayfasını defalarca ziyaret etme konusunda endişelenmemize gerek olmadığıdır. Varsayılan olarak, Scrapy, önceden ziyaret edilen URL'lere çoğaltılmış istekleri filtreler ve bir programlama hatası nedeniyle sunuculara çok fazla vurma sorununu önler. Bu ayar DUPEFILTER_CLASS tarafından yapılandırılabilir.

Umarım şimdiye kadar Scrapy ile bağlantıları ve geri aramaları takip etme mekanizmasını nasıl kullanacağınızı iyi anlarsınız.

Bağlantıları takip etme mekanizmasından yararlanan başka bir örnek örümcek olarak, tarayıcılarınızı üzerine yazmak için kullanabileceğiniz küçük bir kural motoru uygulayan jenerik bir örümcek için Crawl Spider sınıfına göz atın.

Ayrıca, ortak bir desen, geri çağrılara ek veri iletmek için bir hile kullanarak birden fazla sayfadan veri içeren bir öğe oluşturmaktır.

Spider argümanlarını kullanma
======================

Spider'larınıza komut satırı argümanlarını çalıştırırken -a seçeneğini kullanarak sağlayabilirsiniz::

    scrapy crawl quotes -O quotes-humor.json -a tag=humor

Bu argümanlar Örümceğin __ init __ yöntemine aktarılır ve varsayılan olarak örümcek nitelikleri haline gelir.

Bu örnekte, etiket argümanı için sağlanan değer self.tag. Bunu, örümceğinizin yalnızca belirli bir etiketle alıntılar getirmesini sağlamak için kullanabilir ve URL'yi argümana göre oluşturabilirsiniz::

    import scrapy


    class QuotesSpider(scrapy.Spider):
        name = "quotes"

        def start_requests(self):
            url = 'http://quotes.toscrape.com/'
            tag = getattr(self, 'tag', None)
            if tag is not None:
                url = url + 'tag/' + tag
            yield scrapy.Request(url, self.parse)

        def parse(self, response):
            for quote in response.css('div.quote'):
                yield {
                    'text': quote.css('span.text::text').get(),
                    'author': quote.css('small.author::text').get(),
                }

            next_page = response.css('li.next a::attr(href)').get()
            if next_page is not None:
                yield response.follow(next_page, self.parse)


Etiket = mizah argümanını bu örümceğe iletirseniz, sadece mizah etiketindeki URL'leri ziyaret edeceğini fark edeceksiniz, örneğin https://quotes.toscrape.com/tag/humor.

Örümcek argümanlarını ele alma hakkında daha fazla bilgiyi buradan edinebilirsiniz.

Sıradaki adım
==========

Bu eğitim sadece Scrapy'nin temellerini kapsıyordu, ancak burada belirtilmeyen birçok başka özellik var. Başka neyi kontrol et? Scrapy'deki bölüm, en önemli olanlara hızlı bir genel bakış için bir bakış bölümünde.

Komut satırı aracı, örümcekler, seçiciler ve öğreticinin kazınmış verileri modellemek gibi kapsamadığı diğer şeyler hakkında daha fazla bilgi edinmek için Temel kavramlar bölümünden devam edebilirsiniz. Örnek bir projeyle oynamayı tercih ediyorsanız Örnekler bölümünü işaretleyin.

.. _JSON: https://en.wikipedia.org/wiki/JSON
