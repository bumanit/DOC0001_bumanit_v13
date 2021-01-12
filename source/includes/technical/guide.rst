**************************
Хөгжүүлэлтийн орчин бэлдэх
**************************

Github-с кодоо татах, эхлээд ``git`` суулгасан байх шаардлагатай

.. code:: console

    $ git clone https://github.com/bumanit/DOC0001_bumanit_v13.git

Татсан прожектын directory уруу очих

.. code:: console

    $ cd DOC0001_bumanit_v13

``virtualenv`` орчин бэлдэх, эхлээд хэрэгтэй сангуудаа суулгах

.. code:: console

    $ sudo apt-get install python3-pip

    $ python3 -m pip install virtualenv

    $ python3 -m virtualenv env
    
    $ source env/bin/activate

``requirements.txt`` суулгах

.. code:: console

    $ pip install -r requirements.txt

Өөрчлөх гэж байгаа модультай ижил нэртэй ``.rst`` өргөтгөлтэй файл ``modules`` фолдер дотор үүсгэнэ

.. code::

    .
    ├── ...
    ├── ...
    └── module_name.rst

``index.rst`` дотор байгаа ``өөрчлөх гэж байгаа модуль <modules/howto.rst>`` байгааг солих ``өөрчлөх гэж байгаа модуль <modules/module_name.rst>``


``module_name.rst`` хүссэн засвараа хийж ``build`` хийх

.. code:: console

    $ make html

``build/html/index.html`` автоматаар generate хийнэ ``index.html``-г нээж үр дүнгээ харна


.. note::
    - readthedocs-н талаар хэрэгтэй гарын авлагуудыг энд тавьлаа
    - https://sphinx-rtd-theme.readthedocs.io/en/latest/index.html
    - https://docs.readthedocs.io/en/stable/intro/getting-started-with-sphinx.html
    - https://documentation-style-guide-sphinx.readthedocs.io/en/latest/style-guide.html


.. *******************
.. Structural Elements
.. *******************

.. Document Section
.. ================

.. Document Subsection
.. -------------------

.. Document Subsubsection
.. ^^^^^^^^^^^^^^^^^^^^^^

.. Document Paragraph
.. """"""""""""""""""

АМЖИЛТ ХҮСЬЕ