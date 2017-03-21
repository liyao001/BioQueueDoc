Get Started
===========
Prerequisites
-------------
BioQueue can store data on SQLite, which means users can set up BioQueue without an extra database software. However, to achieve a higher performance, we suggest users to install MySQL. For Windows users, download the MySQL Installer or Zipped binary from `MySQL <http://www.mysql.com/downloads/>`_. For POSIX compatible systems (like Ubuntu) users, running the following command should be enough to install MySQL server::

	sudo apt-get install mysql-server mysql-client
	sudo apt-get install libmysqld-dev
	mysql -u root -p
	CREATE DATABASE BioQueue;
	CREATE USER 'bioqueue'@'localhost' IDENTIFIED BY 'YOURPASSWORD';
	GRANT ALL PRIVILEGES ON BioQueue . * TO 'bioqueue'@'localhost';

**Please replace 'YOURPASSWORD' with your own password for the database!**

Since BioQueue is written in Python 2.7, please make sure that you have installed Python and pip. The following instructions are for Ubuntu 14.04, but can be used as guidelines for other Linux flavors::

	sudo apt-get install python-dev
	sudo apt-get install python-pip

Installation
------------
First of all, clone the project from github (Or you can download BioQueue by open this `link <https://github.com/liyao001/BioQueue/zipball/master>`_)::

	git clone https://github.com/liyao001/BioQueue.git
	Or
	wget https://github.com/liyao001/BioQueue/zipball/master

Then navigate to the project's directory, and run the ``install.py`` script (All dependent python packages will be automatically installed)::

	cd BioQueue
	python install.py

When running ``install.py``, this script will ask you a few questions including:

1. CPU cores: The amount of CPU to use. Default value: all cores on that machine.
2. Memory (Gb): The amount of memory to use. Default value: all physical memory on that machine.
3. Disk quota for each user(Gb, default value: all disk space on that machine).

If you decide to run BioQueue with MySQL, the script will ask a few more questions:

1. Database host: If you install MySQL server on your own machine, enter `localhost` or `127.0.0.1`.
2. Database user: user name of the database. *bioqueue* by default.
3. Database password: password of the database.
4. Database name: Name of the data table.
5. Database port: *3306* by default

Start the queue
^^^^^^^^^^^^^^^
Run ``bioqueuepy`` script in the *BioQueue/worker* folder::

	python worker/bioqueue.py

For Linux/Unix users, BioQueue can run in the background by running ``bioqueue_daemon.py`` instead of ``bioqueue.py``::

	python worker/bioqueue_daemon.py start

Start web server
^^^^^^^^^^^^^^^^
Run the following command to start the webserver::

	python manage.py runserver 0.0.0.0:8000

This will start up the server on **0.0.0.0** and port **8000**, so BioQueue can be accessed over the network. If you want access BioQueue only in local environment, remove **0.0.0.0:8000**. If you want to use BioQueue in a production environment, we recommend you to use a proxy server instead of ``manage.py`` (see detail at `FAQ <faq.html#use-bioqueue-with-apache-in-production-environment>`_).

Start FTP server
^^^^^^^^^^^^^^^^
BioQueue provides a FTP server to make it more convenient to transfer files, to run this server::

	python worker/ftpserver.py

This step is optional, if you run command above, the FTP server will listen **20001** port by default. For Linux/Unix users, BioQueue FTP service can run in background by run ``ftp_daemon.py`` instead of ``ftpserver.py``::

	python worker/ftp_daemon.py start

Useful informations
-------------------
1. To stop the queue, the webserver or the FTP server, just hit Ctrl-c in the terminal from which BioQueue is running. If you run the queue or FTP server in the background, hit::

	python worker/bioqueue_daemon.py stop
	python worker/ftp_daemon.py stop

2. To get a better performance, moving the webserver to `Apache <faq.html#use-bioqueue-with-apache-in-production-environment>`_ or `nginx <http://nginx.org/>`_ is a good idea.
