.. _intro-install:

==================
Yükleme Rehberi
==================

.. _faq-python-versions:

Desteklenen Python Sürümleri
=========================
Scrapy, CPython uygulaması veya PyPy 7.2.0+ uygulaması olan Python 3.6+ gerektirir.


Scrapy'yi Yükleme
=================
Anaconda veya Miniconda kullanıyorsanız, paketi Linux, Windows ve macOS için güncel paketlerin bulunduğu conda-forge kanalından yükleyebilirsiniz.

Scrapy'yi yüklemek için conda’yı açın ve şu kodu çalıştırın:

  conda install -c conda-forge scrapy

Alternatif olarak, Python paketlerinin kurulumuna zaten aşina iseniz, Scrapy'yi ve bağımlılıklarını PyPI'den şu şekilde kurabilirsiniz:

    pip install Scrapy



Bilmenizde Fayda Var 
----------------------------

Scrapy, saf Python ile yazılmıştır ve birkaç temel Python paketine bağlıdır:

* `lxml`_, verimli bir XML ve HTML ayrıştırıcısı
* `parsel`_, lxml üzerine yazılmış bir HTML/XML veri çıkarma kitaplığı,
* `w3lib`_, URL'ler ve web sayfası kodlamalarıyla ilgilenmek için çok amaçlı bir yardımcı
* `twisted`_, asenkron bir ağ framework’ü
* `cryptography`_ and `pyOpenSSL`_, Çeşitli ağ düzeyinde güvenlik ihtiyaçlarıyla başa çıkmak için 

Scrapy'nin test edildiği minimal sürümler şunlardır:

* Twisted 14.0
* lxml 3.4
* pyOpenSSL 0.14

Scrapy, bu paketlerin eski sürümleriyle çalışabilir, ancak bunlara karşı test edilmediğinden çalışmaya devam edeceği garanti edilmez.

Some of these packages themselves depends on non-Python packages
that might require additional installation steps depending on your platform.
Please check :ref:`platform-specific guides below <intro-install-platform-notes>`.

In case of any trouble related to these dependencies,
please refer to their respective installation instructions:

* `lxml installation`_
* :doc:`cryptography installation <cryptography:installation>`

.. _lxml installation: https://lxml.de/installation.html


.. _intro-using-virtualenv:

Sanal Ortam Kullanma (Önerilir)
-----------------------------------------

TL;DR: We recommend installing Scrapy inside a virtual environment
on all platforms.

Python packages can be installed either globally (a.k.a system wide),
or in user-space. We do not recommend installing Scrapy system wide.


Scrapy'yi tüm platformlarda sanal bir ortama kurmanızı öneririz.
Python paketleri, global olarak (yani sistem genelinde) veya kullanıcı alanında kurulabilir. Scrapy sistemini geniş bir alana kurmanızı önermiyoruz.
Bunun yerine, Scrapy'yi "sanal ortam" ( venv) olarak adlandırılan bir ortama kurmanızı öneririz. Sanal ortamlar, önceden kurulmuş Python sistem paketleriyle (bazı sistem araçlarınızı ve komut dosyalarınızı bozabilir) çakışmamanıza ve yine de paketleri normal olarak pip yüklemenize izin verir.


See :ref:`tut-venv` on how to create your virtual environment.

Once you have created a virtual environment, you can install Scrapy inside it with ``pip``,
just like any other Python package.
(See :ref:`platform-specific guides <intro-install-platform-notes>`
below for non-Python dependencies that you may need to install beforehand).


.. _intro-install-platform-notes:

Platforma Özel Kurulum Notları
====================================

.. _intro-install-windows:

Windows
-------

Scrapy'yi Windows'a pip kullanarak kurmak mümkün olsa da Anaconda veya Miniconda'yı kurmanızı ve çoğu kurulum sorununu ortadan kaldıracak olan paketi conda-forge kanalından kullanmanızı öneririz.

Once you've installed `Anaconda`_ or `Miniconda`_, install Scrapy with::

  conda install -c conda-forge scrapy

Windows’a "pip" kullanarak Scrapy yüklemek için;

.. warning::
   Bu yükleme yöntemi, Anaconda’dan önemli ölçüde daha fazla disk alanı 
   gerektiren bazı Scrapy bağımlılıklarını yüklemek için “Microsoft Visual C++” gerektirir.

#. Visual Studio Installer'ı yüklemek için Microsoft C++ Build Tools'u indirin ve çalıştırın.

#. Visual Studio Installer'ı çalıştırın.

#. İş Yükleri bölümünün altında, **C++** build tools öğesini seçin.

#. Kurulum ayrıntılarını kontrol edin ve isteğe bağlı bileşenler olarak aşağıdaki paketlerin seçildiğinden emin olun:

    * **MSVC**  (e.g MSVC v142 - VS 2019 C++ x64/x86 build tools (v14.23) )
    
    * **Windows SDK**  (e.g Windows 10 SDK (10.0.18362.0))

#. Visual Studio Build Tools yükleyin.

Şimdi pip kullanarak Scrapy yükleyebilirsiniz.

.. _intro-install-ubuntu:

Ubuntu 14.04 Veya Üzeri
---------------------

Scrapy şu anda lxml, twisted ve pyOpenSSL'nin yeterince yeni sürümleriyle test edilmiştir ve en son Ubuntu dağıtımlarıyla uyumludur. 
Ancak TLS bağlantılarıyla ilgili olası sorunlara rağmen Ubuntu 14.04 gibi eski Ubuntu sürümlerini de desteklemesi gerekir.

Ubuntu tarafından sağlanan python-scrapy paketini **kullanmayın**, genellikle çok eskidirler ve en son Scrapy'yi yakalamak için yavaştırlar.


Scrapy'yi Ubuntu (veya Ubuntu tabanlı) sistemlere kurmak için şu bağımlılıkları kurmanız gerekir::

    sudo apt-get install python3 python3-dev python3-pip libxml2-dev libxslt1-dev zlib1g-dev libffi-dev libssl-dev

- ``python3-dev``, ``zlib1g-dev``, ``libxml2-dev`` and ``libxslt1-dev``
  are required for ``lxml``
- ``libssl-dev`` and ``libffi-dev`` are required for ``cryptography``

Inside a :ref:`virtualenv <intro-using-virtualenv>`,
you can install Scrapy with ``pip`` after that::

    pip install scrapy

.. note::
    Aynı Python dışı bağımlılıklar, Scrapy’yi Debian Jessie (8.0) ve üzeri sürümlere kurmak için kullanılabilir


.. _intro-install-macos:

macOS
-----

Scrapy'nin bağımlılıklarını oluşturmak, bir C derleyicisinin ve geliştirme başlıklarının varlığını gerektirir. macOS'ta bu, genellikle Apple'ın Xcode geliştirme araçları tarafından sağlanır. Xcode komut satırı araçlarını kurmak için bir terminal penceresi açın ve şunu çalıştırın::

    xcode-select --install

Pip'in sistem paketlerini güncellemesini engelleyen bilinen bir sorun var
Scrapy ve bağımlılıklarını başarıyla kurmak için bu sorunun ele alınması gerekir.
İşte önerilen bazı çözümler:


* *•	(Önerilen) Sistem python'u **kullanmayın**, sisteminizin geri kalanıyla çakışmayan yeni, güncellenmiş bir sürüm yükleyin.

  * Install `homebrew`_ following the instructions in https://brew.sh/

  * Update your ``PATH`` variable to state that homebrew packages should be
    used before system packages (Change ``.bashrc`` to ``.zshrc`` accordantly
    if you're using `zsh`_ as default shell)::

      echo "export PATH=/usr/local/bin:/usr/local/sbin:$PATH" >> ~/.bashrc

  * Reload ``.bashrc`` to ensure the changes have taken place::

      source ~/.bashrc

  * Install python::

      brew install python

  * Latest versions of python have ``pip`` bundled with them so you won't need
    to install it separately. If this is not the case, upgrade python::

      brew update; brew upgrade python

*   * (İsteğe bağlı) Scrapy'yi bir Python sanal ortamına kurun.


Bu geçici çözümlerden herhangi birinin ardından, Scrapy'yi yükleyebilmelisiniz::

  pip install Scrapy


PyPy
----

En son PyPy sürümünü kullanmanızı öneririz. 
Test edilen sürüm 5.9.0'dır. PyPy3 için sadece Linux kurulumu test edilmiştir.

Çoğu Scrapy bağımlılığında artık CPython için ikili tekerlekler bulunur, ancak PyPy için yoktur. 
Bu, bu bağımlılıkların kurulum sırasında oluşturulacağı anlamına gelir. macOS'ta, Şifreleme bağımlılığı oluşturmayla ilgili bir sorunla karşılaşmanız olasıdır, 
bu sorunun çözümü burada açıklanmıştır, yani bu komutun önerdiği bayrakları dışa aktarmak (yalnızca Scrapy'yi yüklerken gereklidir). 
Linux'a yüklemenin, derleme bağımlılıkları kurmanın yanı sıra özel bir sorunu yoktur. Windows'ta Scrapy'yi PyPy ile kurmak test edilmemiştir. 

You can check that Scrapy is installed correctly by running ``scrapy bench``.
If this command gives errors such as
``TypeError: ... got 2 unexpected keyword arguments``, this means
that setuptools was unable to pick up one PyPy-specific dependency.
To fix this issue, run ``pip install 'PyPyDispatcher>=2.1.0'``.


.. _intro-install-troubleshooting:

Sorun Giderme
===============

AttributeError: 'module' object has no attribute 'OP_NO_TLSv1_1'
----------------------------------------------------------------

Scrapy, Twisted veya py Open SSL'i yükledikten veya yükselttikten sonra aşağıdaki geri dönüşle bir istisna elde edebilirsiniz::

    […]
      File "[…]/site-packages/twisted/protocols/tls.py", line 63, in <module>
        from twisted.internet._sslverify import _setAcceptableProtocols
      File "[…]/site-packages/twisted/internet/_sslverify.py", line 38, in <module>
        TLSVersion.TLSv1_1: SSL.OP_NO_TLSv1_1,
    AttributeError: 'module' object has no attribute 'OP_NO_TLSv1_1'

Bu istisnayı elde etmenizin nedeni, sisteminizin veya sanal ortamınızın Twisted sürümünüzün 
desteklemediği bir py Open SSL sürümüne sahip olmasıdır.

Twisted sürümünüzün desteklediği py Open SSL sürümünü yüklemek için,
:code:`tls` ekstra seçeneğiyle Twisted uygulamasını yeniden yükleyin::

    pip install twisted[tls]

For details, see `Issue #2473 <https://github.com/scrapy/scrapy/issues/2473>`_.

.. _Python: https://www.python.org/
.. _pip: https://pip.pypa.io/en/latest/installing/
.. _lxml: https://lxml.de/index.html
.. _parsel: https://pypi.org/project/parsel/
.. _w3lib: https://pypi.org/project/w3lib/
.. _twisted: https://twistedmatrix.com/trac/
.. _cryptography: https://cryptography.io/en/latest/
.. _pyOpenSSL: https://pypi.org/project/pyOpenSSL/
.. _setuptools: https://pypi.python.org/pypi/setuptools
.. _homebrew: https://brew.sh/
.. _zsh: https://www.zsh.org/
.. _Anaconda: https://docs.anaconda.com/anaconda/
.. _Miniconda: https://docs.conda.io/projects/conda/en/latest/user-guide/install/index.html
.. _Visual Studio: https://docs.microsoft.com/en-us/visualstudio/install/install-visual-studio
.. _Microsoft C++ Build Tools: https://visualstudio.microsoft.com/visual-cpp-build-tools/
.. _conda-forge: https://conda-forge.org/
