



****************************************************
Prepare Odoo Development Environment
****************************************************

In this guide we assume Odoo 13.0 version



Preparing ubuntu
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

..  code:: console

    $ sudo add-apt-repository universe
    $ sudo add-apt-repository "deb http://mirrors.kernel.org/ubuntu/ xenial main"
    $ sudo apt-get update
    $ sudo apt-get upgrade -y
    


Install ubuntu necessary packages
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

..  code:: console

    $ sudo apt-get update
    $ sudo add-apt-repository ppa:linuxuprising/libpng12
    $ sudo apt update
    $ sudo apt install -y libpng12-0 libjpeg-dev gdebi
    $ sudo apt-get install -y curl git build-essential wget
    $ sudo apt-get install -y libxslt1-dev libzip-dev libldap2-dev libsasl2-dev
    $ sudo apt-get install -y python3-pip python3-dev python3-venv python3-wheel python3-setuptools
    $ sudo apt-get install -y node-less
    $ wget https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.5/wkhtmltox_0.12.5-1.trusty_amd64.deb
    $ sudo dpkg -i wkhtmltox_0.12.5-1.trusty_amd64.deb


Install nodejs
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

..  code:: console

    $ curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -
    $ sudo apt-get -y install nodejs
    $ node --version
    $ npm --version
    $ sudo npm install -g rtlcss



Install postgresql
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


..  code:: console

    $ sudo apt-get install postgresql postgresql-contrib



Pull code from github
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

..  code:: console

    $ git clone --single-branch --branch 13.0 https://github.com/odoo/odoo.git
    $ git clone --single-branch --branch 13.0 https://github.com/bumanit/bumanit_odoo.git



Preparing python virtual environment
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

..  code:: console

    $ python3 -m pip install virtualenv
    $ python3 -m virtualenv venv
    $ source venv/bin/activate
    $ cd odoo
    $ pip install -r requirements.txt



Prepare Odoo config file
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

..  code::

    [options]; 
    db_host = localhost
    db_port = 5432
    db_name = odoo
    db_user = odoo
    db_password = odoo
    addons_path = extra_paths # It's your Odoo path


Run
^^^^^^^^^^^^^^^^^^^^^


..  code:: console

    $ ./odoo-bin -c ../odoo.conf