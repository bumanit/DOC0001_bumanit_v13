


*************************
Configure eBarimt PosAPI
*************************

Posapi нь :guilabel:`eBarimt` системтэй холбогдох сервис бөгөөд энэхүү гарын авлага нь ubuntu 
үйлдлийн систем дээр posapi хэрхэн тохируулж production болгох талаар оруулав.


Тухайн posapi-г ямар систем дээр ашиглахаас хамаарч хоёр өөр газар тохируулна. Odoo 10 систем дээр
байгаа харилцагчийн posapi-г `10.0.0.22`, Odoo 12,13 систем дээр байгаа харилцагчийн posapi-г `10.0.0.66` дээр 
тохиргоог хийнэ.


`10.0.0.22` болон `10.0.0.66` гол ялгаа нь ``python``, ``python3`` юм.


Posapi-г сервер уруу зөөх
===========================

..  code:: console

    $ scp 6030548_001_5864642.zip bumanerp@10.0.0.22:home/bumanerp


Posapi-г сервер дээр тохируулах
================================

.. note::

    1) ``10.0.0.22``, ``10.0.0.66`` дээр хэрэгтэй бүх сангуудыг суулгасан байгаа.
    2) Файл директорийн нэрийг цэгцтэй зарлаарай бусад харилцагчийн тохиргоог хэрхэн хийсэн байгааг бас хараарай.


10.0.0.22 дээрх тохиргоо
-----------------------------


..  code:: console

    $ scp 0000038_100_4101075.zip monossys@10.0.0.22:home/monossys # posapi сервер уруу зөөнө

    $ ssh monossys@10.0.0.22 # сервер уруу хандана

    $ mkdir vatpsp_bridge_build/saas/vat_CHANGE_ME # posapi байрлах үндсэн директори

    $ mkdir vatpsp_bridge_build/saas/vat_CHANGE_ME/vatps_api # posapi header файлууд байрлах директори

    $ mv 0000038_100_4101075.zip vatpsp_bridge_build/saas/vat_CHANGE_ME/vatps_api

    $ cd vatpsp_bridge_build/saas/vat_CHANGE_ME/vatps_api

    $ unzip 0000038_100_4101075.zip

    $ cd ../


bridge.cpp болон CMakeLists.txt файлууд үүсгэх
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


.. note::

    ``bridge.cpp``, ``CMakeLists.txt`` үүсгэхдээ доорх жишээ кодуудыг хуулаад `CHANGE_ME` текстийг солиод
    шинэ ``bridge.cpp``, ``CMakeLists.txt`` файлууд уруу хуулна. Үүсгэсэн үндсэн директорийг кодон дунд
    заасан болохоор директорийн нэрээр солино. Бусад харилцагчийн тохиргоог хэрхэн хийсэн талаар сайн
    хараарай нэг бүрчлэн тайлбарлах боломжгүй логикүүд явж байгаа болохоор дуурайлгаад хийгээрэй.

``bridge.cpp`` файл

.. code:: c++
    
    #include "PosAPI.h"
    #include <Python.h>

    #include <iostream>
    #include <fstream>

    using namespace vatps;
    using namespace std;

    static PyObject * vat_PutData(PyObject *self, PyObject *args)
    {
        const char *putData;
        string put_result;
        int sts;

        if (!PyArg_ParseTuple(args, "s", &putData)){
            Py_INCREF(Py_None);
            return Py_None;
        }
        put_result = PosAPI::put(putData);;

        return PyUnicode_FromString(put_result.c_str());
    }

    static PyObject * vat_returnBill(PyObject *self, PyObject *args)
    {
        const char *returnData;
        string return_result;
        int sts;

        if (!PyArg_ParseTuple(args, "s", &returnData)){
            Py_INCREF(Py_None);
            return Py_None;
        }
        return_result = PosAPI::returnBill(returnData);;

        return PyUnicode_FromString(return_result.c_str());
    }

    static PyObject * vat_callFunction(PyObject *self, PyObject *args)
    {
        const char *functionParam;
        const char *function_name;
        string put_result;
        int sts;

        if (!PyArg_ParseTuple(args, "s", &function_name, &functionParam)){
            Py_INCREF(Py_None);
            return Py_None;
        }
        put_result = PosAPI::callFunction(function_name, functionParam);

        return PyUnicode_FromString(put_result.c_str());
    }
    static PyObject * vat_SendData(PyObject *self, PyObject *args)
    {
        string send_result;
        send_result = PosAPI::sendData();;

        return PyUnicode_FromString(send_result.c_str());
    }

    static PyObject * vat_checkApi(PyObject *self, PyObject *args)
    {
        string check_result;
        check_result = PosAPI::checkApi();;
        return PyUnicode_FromString(check_result.c_str());
    }

    static PyObject * vat_getInformation(PyObject *self, PyObject *args)
    {
        string getinfo_result;
        getinfo_result = PosAPI::getInformation();;
        return PyUnicode_FromString(getinfo_result.c_str());
    }

    static PyMethodDef VatBridgeMethods[] = {
        // ...
        {"sendData",  vat_SendData, METH_VARARGS, "Send Transaction data."},
        {"putData",  vat_PutData, METH_VARARGS, "VAT Bridge System Put."},
        {"returnBill",  vat_returnBill, METH_VARARGS, "VAT Bridge System returnBill."},

        {"checkApi", vat_checkApi, METH_NOARGS, "VAT Bridge System check API"},
        {"getInformation", vat_getInformation, METH_NOARGS, "VAT Bridge System Get Information"},
        {"callFunction", vat_callFunction, METH_VARARGS, "VAT Bridge System call Extra Functions"},

        // ...
        {NULL, NULL, 0, NULL}        /* Sentinel */
    };

    PyMODINIT_FUNC
    initvat_CHANGE_ME(void)
    {
        Py_InitModule("vat_CHANGE_ME", VatBridgeMethods);
    }

    int
    main(int argc, char *argv[])
    {
        /* Add a built-in module, before Py_Initialize */
        PyImport_AppendInittab("vat_CHANGE_ME", initvat_CHANGE_ME);

        /* Pass argv[0] to the Python interpreter */
        // Py_SetProgramName(program_name);

        /* Initialize the Python interpreter.  Required. */
        Py_Initialize();

        string send_result = PosAPI::sendData();
        cout << "Send RESULT : " << send_result << endl;

        // cout << "RESULT : " << result << endl;

    }


``CMakeLists.txt`` файл

.. code:: cmake

    cmake_minimum_required(VERSION 2.8)
    project(vat_CHANGE_ME)

    set(CMAKE_INSTALL_PREFIX "/usr")
    set(CMAKE_PREFIX_PATH "/usr")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -fPIC")
    set(CMAKE_BUILD_TYPE Release)

    find_package(PythonLibs 2.7 REQUIRED)
    find_package(Qt5Widgets REQUIRED)
    find_package(Qt5Sql REQUIRED)

    include_directories(${CMAKE_CURRENT_SOURCE_DIR}/vatps_api/include)
    include_directories(${PYTHON_INCLUDE_DIRS})

    file(GLOB ALL_SRC "*.cpp")
    file(GLOB ALL_HEADER "include/*.h")

    link_directories(${CMAKE_CURRENT_SOURCE_DIR}/vatps_api/x64)

    set(CMAKE_INCLUDE_CURRENT_DIR ON)

    set(SOURCE_FILES ${ALL_SRC} ${ALL_HEADER})

    add_library(vat_CHANGE_ME SHARED ${SOURCE_FILES})


    set_target_properties(vat_CHANGE_ME PROPERTIES PREFIX "")

    target_link_libraries(vat_CHANGE_ME ${PYTHON_LIBRARIES})
    target_link_libraries(vat_CHANGE_ME "PosAPI")

    qt5_use_modules(vat_CHANGE_ME Widgets PrintSupport Network WebKitWidgets Sql)


.. code: console

    $ vi bridge.cpp # Шинэ bridge.cpp файл үүсгэнэ

    $ vi CMakeLists.txt # Шинэ CMakeLists.txt файл үүсгэнэ


``bridge.cpp``, ``CMakeLists.txt`` файлууд үүссэн бол доорх коммандуудыг ажиллуулвал ``vat_CHANGE_ME.so`` файл үүснэ.

.. code:: console

    $ cmake .

    $ make

    $ python

``vat_CHANGE_ME.so`` файлыг шалгах

.. code:: python

    import vat_CHANGE_ME as v

    v.sendData() # Хариу ирж байвал амжилттай болсон гэсэн үг.
    v.getInformation() # Хариу ирж байвал амжилттай болсон гэсэн үг.


Бридж кодын тохиргоо
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

..  code:: console

    $ cd /var/www/vatservice
    
    $ sudo cp vatservice_dcstore.wsgi vatservice_CHANGE_ME.wsgi
    
    $ sudo vi vatservice_CHANGE_ME.wsgi # доорх wsgi файлын хуулж CHANGE_ME-г солиорой


..  code:: python

    import os, sys; sys.path.append(os.path.dirname(__file__)) 
    from vatservice_main import create_app
    access_token = 'bhxu70NSSVgivWZtSOpY7pkxz5Ymms54'
    application = create_app('/home/monossys/vatpsp_bridge_build/saas/vat_CHANGE_ME','vat_CHANGE_ME','/home/monossys/log/vatbridge.log', False)


Apache conf тохируулах
^^^^^^^^^^^^^^^^^^^^^^


Apache-д байрлуулахдаа port нээх бөгөөд доор жишээгээр үзүүллээ


..  code:: console

    $ sudo vi /etc/apache/ports.conf # apache listen port нэмэх 7000-с эхэлж байгаа

    $ sudo vi /etc/apache2/sites-available/vatbridges.conf # доорх apache tag нэмэх


``vatbridges.conf`` доорх кодыг нэмнэ.

..  code::  apache

    <VirtualHost *:port>
        ServerName 10.0.0.22
        WSGIDaemonProcess vatservice_CHANGE_ME user=monossys group=monossys processes=2 threads=5
        WSGIScriptAlias / /var/www/vatservice/vatservice_CHANGE_ME.wsgi

        <Directory /var/www/vatservice>
            WSGIProcessGroup vatservice_CHANGE_ME
            WSGIApplicationGroup %{GLOBAL}
            Order deny,allow
            Allow from all
        </Directory>

        ErrorLog /var/log/apache2/vatservices-error.log
        CustomLog /var/log/apache2/vatservices-access.log combined

    </VirtualHost>


..  code:: console

    $ sudo service apache2 restart # Ингээд л тохиргоо дууслаа

.. note::

    Харилцагчийн ИРП уруу орж систем параметр дээр posapi-г холбож өгнө. Бусад харилцагч дээр 
    хэрхэн тохируулсан байгааг хараад дуурайлгаад хийгээрэй.



10.0.0.66 дээрх тохиргоо
-----------------------------

Odoo 12, 13 дээр нэвтрүүлсэн харилцагчийн posapi тохиргоог энд хийнэ.

..  code:: console

    $ scp 0000038_100_4101075.zip bumanerp@10.0.0.66:home/bumanerp # posapi сервер уруу зөөнө

    $ ssh bumanerp@10.0.0.66 # сервер уруу хандана

    $ mkdir vatapi/vat_CHANGE_ME # posapi байрлах үндсэн директори

    $ mkdir vatapi/vat_CHANGE_ME/vatps_api # posapi header файлууд байрлах директори

    $ mv 0000038_100_4101075.zip vatapi/vat_CHANGE_ME/vatps_api

    $ cd vatapi/vat_CHANGE_ME/vatps_api

    $ unzip 0000038_100_4101075.zip

    $ cd ../


bridge.cpp болон CMakeLists.txt файлууд үүсгэх
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


.. note::

    ``bridge.cpp``, ``CMakeLists.txt`` үүсгэхдээ доорх жишээ кодуудыг хуулаад `CHANGE_ME` текстийг солиод
    шинэ ``bridge.cpp``, ``CMakeLists.txt`` файлууд уруу хуулна. Үүсгэсэн үндсэн директорийг кодон дунд
    заасан болохоор директорийн нэрээр солино. Бусад харилцагчийн тохиргоог хэрхэн хийсэн талаар сайн
    хараарай нэг бүрчлэн тайлбарлах боломжгүй логикүүд явж байгаа болохоор дуурайлгаад хийгээрэй.

``bridge.cpp`` файл

.. code:: c++
    
    #include "PosAPI.h"
    #include <Python.h>

    #include <iostream>
    #include <fstream>

    using namespace vatps;
    using namespace std;

    static PyObject * vatbridge_PutData(PyObject *self, PyObject *args)
    {
        const char *putData;
        string put_result;

        if (!PyArg_ParseTuple(args, "s", &putData)){
            Py_INCREF(Py_None);
            return Py_None;
        }
        put_result = PosAPI::put(putData);;

        return PyUnicode_FromString(put_result.c_str());
    }

    static PyObject * vatbridge_returnBill(PyObject *self, PyObject *args)
    {
        const char *returnData;
        string return_result;

        if (!PyArg_ParseTuple(args, "s", &returnData)){
            Py_INCREF(Py_None);
            return Py_None;
        }
        return_result = PosAPI::returnBill(returnData);;

        return PyUnicode_FromString(return_result.c_str());
    }

    static PyObject * vatbridge_callFunction(PyObject *self, PyObject *args)
    {
        const char *functionParam;
        const char *function_name;
        string put_result;

        if (!PyArg_ParseTuple(args, "s", &function_name, &functionParam)){
            Py_INCREF(Py_None);
            return Py_None;
        }
        put_result = PosAPI::callFunction(function_name, functionParam);

        return PyUnicode_FromString(put_result.c_str());
    }
    static PyObject * vatbridge_SendData(PyObject *self, PyObject *args)
    {
        string send_result;
        send_result = PosAPI::sendData();;

        return PyUnicode_FromString(send_result.c_str());
    }

    static PyObject * vatbridge_checkApi(PyObject *self, PyObject *args)
    {
        string check_result;
        check_result = PosAPI::checkApi();;
        return PyUnicode_FromString(check_result.c_str());
    }

    static PyObject * vatbridge_getInformation(PyObject *self, PyObject *args)
    {
        string getinfo_result;
        getinfo_result = PosAPI::getInformation();;
        return PyUnicode_FromString(getinfo_result.c_str());
    }

    static PyMethodDef VatBridgeMethods[] = {
        // ...
        {"sendData",  vatbridge_SendData, METH_VARARGS, "Send Transaction data."},
        {"putData",  vatbridge_PutData, METH_VARARGS, "VAT Bridge System Put."},
        {"returnBill",  vatbridge_returnBill, METH_VARARGS, "VAT Bridge System returnBill."},

        {"checkApi", vatbridge_checkApi, METH_NOARGS, "VAT Bridge System check API"},
        {"getInformation", vatbridge_getInformation, METH_NOARGS, "VAT Bridge System Get Information"},
        {"callFunction", vatbridge_callFunction, METH_VARARGS, "VAT Bridge System call Extra Functions"},

        // ...
        {NULL, NULL, 0, NULL}        /* Sentinel */
    };

    static struct PyModuleDef VatBridgeModuleDef = {
            PyModuleDef_HEAD_INIT,
            "vatps_CHANGE_ME", // module name
            "",     // description of module
            -1,     // size of per-interpreter state of the module
            VatBridgeMethods
    };



    PyMODINIT_FUNC
    PyInit_vatps_CHANGE_ME(void)
    {
    //    cout << "Hello From C " << endl;
        //Py_InitModule("vatps_CHANGE_ME", VatBridgeMethods);
        return PyModule_Create(&VatBridgeModuleDef);
    }

    int
    main(int argc, char *argv[])
    {
        /* Add a built-in module, before Py_Initialize */
        PyImport_AppendInittab("vatps_CHANGE_ME", PyInit_vatps_CHANGE_ME);

        /* Pass argv[0] to the Python interpreter */
        // Py_SetProgramName(program_name);

        /* Initialize the Python interpreter.  Required. */
        Py_Initialize();

        string send_result = PosAPI::sendData();
        cout << "Send RESULT : " << send_result << endl;


    }


``CMakeLists.txt`` файл

.. code:: cmake

    cmake_minimum_required(VERSION 2.8)

    project(vatps_CHANGE_ME)

    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -std=c++11 -fPIC -D_GLIBCXX_USE_CXX11_ABI=0")

    set(CMAKE_BUILD_TYPE Release)

    find_package(PythonLibs 3 EXACT)

    find_package(Qt5Widgets REQUIRED)

    find_package(Qt5Sql REQUIRED)

    include_directories(${CMAKE_CURRENT_SOURCE_DIR}/vatps_api/include)
    include_directories(${PYTHON_INCLUDE_DIRS})

    file(GLOB ALL_SRC "*.cpp")
    file(GLOB ALL_HEADER "include/*.h")

    link_directories(${CMAKE_CURRENT_SOURCE_DIR}/vatps_api/x64)

    set(CMAKE_INCLUDE_CURRENT_DIR ON)

    set(SOURCE_FILES ${ALL_SRC} ${ALL_HEADER})

    add_library(vatps_CHANGE_ME SHARED ${SOURCE_FILES})

    set_target_properties(vatps_CHANGE_ME PROPERTIES PREFIX "")

    target_link_libraries(vatps_CHANGE_ME ${PYTHON_LIBRARIES})
    target_link_libraries(vatps_CHANGE_ME "PosAPI")
    target_link_libraries(vatps_CHANGE_ME "sqlite3")

    qt5_use_modules(vatps_CHANGE_ME PrintSupport Network Sql)


.. code: console

    $ vi bridge.cpp # Шинэ bridge.cpp файл үүсгэнэ

    $ vi CMakeLists.txt # Шинэ CMakeLists.txt файл үүсгэнэ


``bridge.cpp``, ``CMakeLists.txt`` файлууд үүссэн бол шинэ ``vatps_CHANGE_ME.so`` файл үүсгэх.

.. code:: console

    $ cmake .

    $ make

``vatps_CHANGE_ME.so`` файлыг шалгах

.. code:: console

    $ python3

.. code:: python

    import vat_CHANGE_ME as v

    v.sendData() # Хариу ирж байвал амжилттай болсон гэсэн үг.
    v.getInformation() # Хариу ирж байвал амжилттай болсон гэсэн үг.


Бридж кодын тохиргоо
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

..  code:: console

    $ cd /var/www/vatservice
    
    $ sudo cp -r vatps_demo vatps_CHANGE_ME
    
    $ cd vatps_CHANGE_ME

    $ sudo rm vatbridge_main.py

    $ sudo ln -s /var/www/vatservice/vatps_CHANGE_ME/vatps_webservice/vatbridge_main_py3.py /var/www/vatservice/vatps_CHANGE_ME/vatbridge_main.py

    $ sudo vi vatbridge_service.wsgi


``vatbridge_service.wsgi`` файлыг засах


.. code:: python

    import os, sys; sys.path.append(os.path.dirname(__file__))
    from vatbridge_main import create_app
    access_token = 'bhxu70NSSVgivWZtSOpY7pkxz5Ymms54'
    application = create_app('/home/bumanerp/vatapi/vatps_CHANEME', 'vatps_CHANEME', '/home/bumanerp/log/vatps_CHANEME.log', access_token)



Apache conf тохируулах
^^^^^^^^^^^^^^^^^^^^^^

| ``10.0.0.22``-с ялгаатайн port-оор биш url path-аар салгасан байгаа



| ``vatservice.conf`` файлыг засах

..  code:: console

    $ sudo vi /etc/apache2/sites-available/vatservice.conf # доорх apache tag нэмэх

..  code:: apache

    #######################################################################################################
    WSGIDaemonProcess vatps_CHANGE_ME user=bumanerp python-home=/home/bumanerp/env
    WSGIProcessGroup vatps_CHANGE_ME
    WSGIScriptAlias /vatps_CHANGE_ME /var/www/vatservice/vatps_CHANGE_ME/vatbridge_service.wsgi process-group=vatps_CHANGE_ME application-group=%{GLOBAL}

    <Directory /var/www/vatservice/vatps_CHANGE_ME>
        Order deny,allow
        Allow from all
        LogLevel info
        <Files wsgi.py>
            Require all granted
        </Files>
    </Directory>
    #######################################################################################################


..  code:: console

    $ sudo service apache2 restart # That's it^^^^^^


.. note::

    Бусад харилцагч дээр хэрхэн тохируулсан байгааг хараад дуурайлгаад хийгээрэй.