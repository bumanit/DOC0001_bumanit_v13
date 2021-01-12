


****************************
Docker run test environment
****************************

..  code:: console

    $ ssh uuganbat@10.0.0.173 # access your domain control
    
    $ cd /opt/odoo13 # go to dir

    $ sudo lsof -i -P -n | grep LISTEN # check for running ports

    $ ./run test13-uuganbat 8070 13.0-development

    $ sudo docker logs -f test13-uuganbat # Лог файл шалгах

#) :guilabel:`test13-uuganbat` database, docker image, docker container name. This is same as your development database
#) :guilabel:`8070` running port. Should not have the same running port.
#) :guilabel:`13.0-development` building git branch name.