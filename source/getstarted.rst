Get Started
===========
Prerequisites
-------------
BioQueue stores data on MySQL, for Windows Users, download the MySQL Installer or Zipped binary from `MySQL <http://www.mysql.com/downloads/>`_. For POSIX compatible systems (like Ubuntu 14.04) users, running following command should be enough to install MySQL server::

	sudo apt-get install mysql-server mysql-client
	sudo apt-get install libmysqld-dev
	mysql -u root -p
	CREATE DATABASE BioQueue;
	CREATE USER 'bioqueue'@'localhost' IDENTIFIED BY 'REPLACE WITH YOUR OWN PASSWORD HERE';
	GRANT ALL PRIVILEGES ON BioQueue . * TO 'bioqueue'@'localhost';

Since BioQueue is written in Python 2.7, please make sure that you have installed Python and pip. The following instructions are for Ubuntu 14.04, but can be used as guidelines for other Linux flavors::

	sudo apt-get install python-dev
	sudo apt-get install python-pip

Installation
------------
First of all, clone the project from github (Or you can download BioQueue by open this `link <https://github.com/yauli/BioQueue/zipball/master>`_)::

	git clone https://github.com/yauli/BioQueue.git
	Or
	wget https://github.com/yauli/BioQueue/zipball/master

Then navigate to the project's directory, and run the ``install.py`` script (All dependent python packages will be automatically installed)::

	cd BioQueue
	python install.py

When running ``install.py``, this script will ask you a few questions including:

1. Path of the workspace (where stores all data for running BioQueue, like *D:/biodata* or */mnt/biodata*).
2. Database host: If you install MySQL server on your own machine, enter *localhost* or *127.0.0.1*.
3. Database user: *bioqueue* by default.
4. Database password
5. Database port: *3306* by default.
6. CPU cores: The amount of CPU to use. Default value: all cores on that machine.
7. Memory (Gb): The amount of memory to use.
8. Disk quota for each user(Gb): The amount of disk to use.

Start the queue
^^^^^^^^^^^^^^^
Run ``cron.py`` script in the *BioQueue/worker* folder::

	python worker/cron.py

For Linux/Unix users, BioQueue can run in the background by running ``queue_daemon.py`` instead of ``cron.py``::

	python worker/queue_daemon.py start

Start web server
^^^^^^^^^^^^^^^^
Run the following command to start the webserver::

	python manage.py runserver 0.0.0.0:8000

This will start up the server on **0.0.0.0** and port **8000**, so BioQueue can be accessed over the network. If you want access BioQueue only in local environment, remove '0.0.0.0:8000'. If you want to use BioQueue in a production environment, we recommend you to use a proxy server instead of ``manage.py`` (see detail at `FAQ <faq.html#use-bioqueue-with-apache-in-production-environment>`_).

Start FTP server
^^^^^^^^^^^^^^^^
BioQueue provides a FTP server to make it more convenient to transfer files, to run this server::

	python worker/ftpserver.py

This step is optional, if you run command above, the FTP server will listen **20001** port by default. For Linux/Unix users, BioQueue FTP service can run in background by run ``ftp_daemon.py`` instead of ``ftpserver.py``::

	python worker/ftp_daemon.py start

Useful informations
-------------------
1. To stop the queue, the webserver or the FTP server, just hit Ctrl-c in the terminal from which BioQueue is running. If you run the queue or FTP server in the background, hit::

	python worker/queue_daemon.py stop
	python worker/ftp_daemon.py stop

2. To get a better performance, moving the webserver to `Apache <https://github.com/yauli/BioQueue/wiki/Use-CPBQueue-with-Apache-and-mod_wsgi>`_ or `nginx <http://nginx.org/>`_ is a good idea.
