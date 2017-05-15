FAQ
===
Upload file
-----------
When uploading datasets, the files are usually cached in memory till the upload is finished. In bioinformatics research, files are large in most cases, so it may eat up memory. To avoid this risk, BioQueue provide a FTP service. The administrator can start the service by running::

  python ftp_daemon.py start
  Or
  python ftpserver.py

And users can use a ftp client (`FileZilla <https://filezilla-project.org/>`_, the free FTP solution) to access this service. Files will be uploaded to user's upload folder, and those files can be selected by click the ``Choose FTP file`` in the ``Create Job`` page. Note: the user name and password are identical to those in BioQueue web platform. The default port is **20001** not **21**.

.. image:: https://cloud.githubusercontent.com/assets/17058337/24163553/e221c5c6-0ea5-11e7-94a6-396d237aa9e3.png

How to update BioQueue
----------------------
We will bring new features and fix bugs when we release a new version. So we recommand you to keep your instance as new as possible. If you have an BioQueue repository and want to update it, run::

  git pull

Use BioQueue with Apache in Production Environment
--------------------------------------------------
To host BioQueue with Apache, you must first install the ``mod_wsgi`` Apache module. On Ubuntu, you can install this as follows::

    sudo apt-get install libapache2-mod-wsgi

If you have not installed Apache, before running command above, you need to setup Apache server by running this command::

    sudo apt-get install apache2

You can then find an example VirtualHost file located at ``deploy/000-default.conf``. Copy this file to ``/etc/apache2/sites-available/`` and restart the Apache server by running (on Ubuntu)::

    sudo /etc/init.d/apache2 restart

Note: For virtualenv users, please replace ``/usr/lib/python2.7/dist-packages`` with ``/path/to/venv/lib/python2.7/site-packages``.

Cannot install MySQL-python?
----------------------------
By default, BioQueue will use a python package called MySQL-python to connect to MySQL server. However, it may be hard to install it especially for non-root users. The alternative solution is to use PyMySQL (a pure python mysql client). Here is the protocol:

1. Remove ``MySQL-python>=1.2.5`` from ``prerequisites.txt`` in ``deploy`` folder.
2. Rerun ``install.py``.
3. Copy the following code and paste them into ``manage.py`` and ``worker >> __init__.py``::
  try:
    import pymysql
    
    pymysql.install_as_MySQLdb()
  except ImportError:
    pass
4. Restart BioQueue.

Turn on E-mail Notification
---------------------------
We know that keeping an eye on watching those time-consuming jobs is very tedious, so BioQueue provides an E-mail notification for changes among job status. By default, e-mail notification is silent. To turn on this push service, you need to fill in a form in ``Settings >> Notification``. If the ``Mail host`` textbox is left to be blank, then the service will be silent, otherwise BioQueue will send a mail to you when a job is finished or an error is raised. And then you can configure it as a mail client.

Using BioQueue in Compute Cluster
---------------------------------
BioQueue possesses a plug-in system to provide supports for compute clusters. For example, BioQueue currently supports TORQUE PBS, and does not require a dedicated or special cluster configuration. If you want to use BioQueue in TORQUE PBS, open the ``Settings >> Cluster Settings``, then fill in the form.

Develop new Cluster Plug-in for BioQueue
----------------------------------------
We use ``__import__`` feature to load drivers for HPC, so to develop new cluster plug-in for BioQueue, you need to implement functions listed in ``worker >> cluster_models >> TorquePBS.py``. Then you should save this driver file in the ``cluster_models`` folder. When you want to load this driver, change ``type`` in ``config.conf`` file to identical to the driver’s name.
