FAQ
===
Use BioQueue with Apache in Production Environment
--------------------------------------------------
To host CPBQueue with Apache, you must first install the ``mod_wsgi`` Apache module. On Ubuntu, you can install this as follows::

    sudo apt-get install libapache2-mod-wsgi

If you have not installed Apache, before run command above, you need to setup Apache server by running this command::

    sudo apt-get install apache2

You can then find an example VirtualHost file located at ``deploy/000-default.conf``. Copy this file to ``/etc/apache2/sites-available/`` and restart the Apache server by running (on Ubuntu)::

    sudo /etc/init.d/apache2 restart

Note: For virtualenv users, please replace ``/usr/lib/python2.7/dist-packages`` with ``/path/to/venv/lib/python2.7/site-packages``.

Turn on E-mail Notification
---------------------------
We know that keeping an eye on watching those time-consuming jobs is very tedious, so BioQueue provides an E-mail notification for changes among job status. By default, e-mail notification is silent. To turn on this push service, you need to fill in a form in ``Settings >> Notification``. If the ``Sender name`` textbox is left to be blank, then the service will be silent, otherwise BioQueue will send a mail to you when a job is finished or an error is raised. And then you can configure it as a mail client.

Using BioQueue in Compute Cluster
---------------------------------
BioQueue possesses a plug-in system to provide supports for compute clusters. For example, BioQueue currently supports TORQUE PBS, and does not require a dedicated or special cluster configuration. If you want to use BioQueue in TORQUE PBS, open the ``config.conf`` file in ``worker`` folder, then change following items in ``torque`` section:

1. ``type`` to ``torque``.
2. ``cpu`` to the amount of CPU to use.
3. ``queue`` to the name of the queue, for example 'high', 'low' or 'fat'.

Develop new Cluster Plug-in for BioQueue
----------------------------------------
To develop new cluster plug-in for BioQueue, you need to implement ``ClusterModel`` in ``worker >> cluster_models >> cluster_model.py`` and override methods defined in it.

+------------------+---------------------------------------------------------+----------------------------------------+
|Method            |Parameter                                                |Return                                  |
+==================+=========================================================+========================================+
|alter_attribute   |attribute                                                |Success: 1, error: 0                    |
+------------------+---------------------------------------------------------+----------------------------------------+
|cancel_job        |None                                                     |Success: 1, error: 0                    |
+------------------+---------------------------------------------------------+----------------------------------------+
|get_cluster_status|None                                                     |A dictionary stores status of every node|
+------------------+---------------------------------------------------------+----------------------------------------+
|hold_job          |None                                                     |Success: 1, error: 0                    |
+------------------+---------------------------------------------------------+----------------------------------------+
|load_template     |None                                                     |A string (template for submitting a job)|
+------------------+---------------------------------------------------------+----------------------------------------+
|query_job_status  |None                                                     |Job id or 0 (error)                     |
+------------------+---------------------------------------------------------+----------------------------------------+
|release_job       |None                                                     |Success: 1, error: 0                    |
+------------------+---------------------------------------------------------+----------------------------------------+
|submit_job        |protocol, job_id, job_step, cpu=0, queue='', workspace=''|Success: 1, error: 0                    |
+------------------+---------------------------------------------------------+----------------------------------------+
