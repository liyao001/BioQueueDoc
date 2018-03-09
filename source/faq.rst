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
We will bring new features and fix bugs when we release a new version. So we recommand you to keep your instance as new as possible. If you have an BioQueue repository and want to update it, there are several ways to do so.

1. Run update.py in worker folder
+++++++++++++++++++++++++++++++++
We provide a python script named as ``update.py`` in ``worker`` folder, which will check updates for both BioQueue's source code and dependent packages::

  python worker/update.py

Also, for Linux/Unix users, BioQueue update service can run in background by run ``update_daemon.py`` instead of ``update.py``::

  python worker/update_daemon.py start

This service will check for update everyday.

2. Click Update button in ``Settings`` page
+++++++++++++++++++++++++++++++++++++++++++
We also provide an update button in the ``Settings`` page, clicking the button, BioQueue will call ``update.py`` to update your instance.

3. git pull
+++++++++++
You can also use git pull command to update BioQueue's source code, but this command won't update the dependent packages!

4. NOTE
+++++++
The update service relies on git, so please make sure that you have installed git and you cloned BioQueue from GitHub.

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
By default, BioQueue will use a python package called MySQL-python to connect to MySQL server. However, it may be hard to install it especially for non-root users. The alternative solution is to use PyMySQL (a pure python mysql client). We provide a python script in BioQueue's ``deploy`` folder to help you to complete the switch. So for most of our users, the following command should be enough to solve this problem::

  python deploy/switch_from_MySQLdb_to_PyMySQL.py

However, if you want to try it yourself, here is the protocol:

1. Remove ``MySQL-python==1.2.5`` from ``prerequisites.txt`` in ``deploy`` folder.
2. Copy the python code and paste them into the begining of ``manage.py`` and ``worker >> __init__.py``:
3. Rerun ``install.py``.

Code::

  try:
      import pymysql
      pymysql.install_as_MySQLdb()
  except ImportError:
      pass

Cannot access BioQueue due to firewall settings
-------------------------------------------------
Sometimes, the firewall installed by the administrators of the cluster may block web access outside the cluster. Under this circumatance, build a tunnel by hiring ``ssh`` should solve the problem. Here is an example::

  ssh -N -L ${PORT}:localhost:${PORT} ${USER_NAME}@{REMOTE_ADDRESS}

Please replace ``${PORT}``, ``${USER_NAME}``, ``${REMOTE_ADDRESS}`` with your own answers. If you use BioQueue's default settings, ``${PORT}`` should be replaced with ``8888``.

Turn on E-mail Notification
---------------------------
We know that keeping an eye on watching those time-consuming jobs is very tedious, so BioQueue provides an E-mail notification for changes among job status. By default, e-mail notification is silent. To turn on this push service, you need to fill in a form in ``Settings >> Notification``. If the ``Mail host`` textbox is left to be blank, then the service will be silent, otherwise BioQueue will send a mail to you when a job is finished or an error is raised. And then you can configure it as a mail client.
