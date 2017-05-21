Cluster Specification
=====================
When running on cluster, BioQueue corporates with the original DRMs and associates them to allocate proper resources for jobs. In this page, we will introduce:

1. How to use BioQueue in cluster
2. How to develop new cluster plugins for BioQueue
3. Problems you may encounter with


How to use BioQueue on clusters
-------------------------------
The usage of BioQueue on clusters is identical to local or clouds. The only
thing you need to do extraly is to tell BioQueue you are using a cluster. Here
is the protocol.

1. Install BioQueue following the `protocol mentioned before <getstarted.html>`_.
2. Run BioQueue web server.
3. Login to BioQueue and open ``Settings``.
4. Click ``Cluster Settings`` in the page and fill in the form. By default, the value for ``Cluster engine`` is ``Run on local / cloud`` and all options for clusters are disabled. Once you choose a cluster engine (For example, TorquePBS), the cluster model for BioQueue will be activated. To turn it off, change cluster engine back to ``Run on local / cloud``.
5. Click ``Update`` to save your changes.
6. *Start the queue*.

In ``Cluster Settings`` section, we provide some options. Here is a more
detailed explanation for them.

+------------------------+------------------------------------------------------------------------------------------------------------------------------+--------------+
|Option                  |Description                                                                                                                   |Default       |
+========================+==============================================================================================================================+==============+
|CPU cores for single job|Specify the number of virtual processors (a physical core on the node or an "execution slot") per node requested for this job.|1             |
+------------------------+------------------------------------------------------------------------------------------------------------------------------+--------------+
|Physicial memory        |Maximum amount of physical memory used by any single process of the job.                                                      |No limit      |
+------------------------+------------------------------------------------------------------------------------------------------------------------------+--------------+
|Virtual memory          |Maximum amount of virtual memory used by all concurrent processes in the job.                                                 |No limit      |
+------------------------+------------------------------------------------------------------------------------------------------------------------------+--------------+
|Destination             |Defines the destination of the job. The destination names a queue, a server, or a queue at a server.                          |Default server|
+------------------------+------------------------------------------------------------------------------------------------------------------------------+--------------+
|Wall-time               |Maximum amount of real time during which the job can be in the running state.                                                 |No limit      |
+------------------------+------------------------------------------------------------------------------------------------------------------------------+--------------+

For example, when BioQueue submits job on a cluster managed by TorquePBS, the options defined above will be translated into Torque parameters like this:

1. -l ppn: CPU cores BioQueue predicts the job will take, if the prediction model has not been generated, the ppn option is equal to ``CPU cores for single job``.
2. -l mem: The physical memory BioQueue predicts the job will use, if the prediction model has not been generated, the mem option is equal to ``Physicial memory``.
3. -l vmem: The virtual memory BioQueue predicts the job will use, if the prediction model has not been generated, the vmem option is equal to ``Virtual memory``.
4. -q: The destination defined in ``Destination``, for example, if the cluster has four queues: high, low, FAT_HIGH, BATCH and middle, the destination should be one of them.
5. -l walltime: Maximum amount of real time during which job can be in the running state defined in ``Wall-time``. For example, 24:00:00.

How to develop new cluster plugins for BioQueue
------------------------------------------------
The support for clusters depends on the corresponding plugins. All python files
in the folder ``worker>>cluster_models`` will be seen as a plugin. BioQueue can
work with these plugins directly and users do not need to modify any code of
BioQueue. However, limited by the clusters we can access, BioQueue now just
provides build-in plugins for TorquePBS (carefully tested under production
environment) and HTCondor (not tested under production environment). So we hope
our users who have the privilege to access other types of DRMs can involve in
the development of cluster plugins. Here we provide a detailed documentation for
developing new plugins for BioQueue.

1. Coding conventions
+++++++++++++++++++++

1. **Required**: All plugins files are written in Python or at least provide a wrapper written in Python. The file name should be identical to the DRM's name and other supplementary files should use the DRM's name with different suffix. Take TorquePBS for example, the plugin file should be named as *TorquePBS.py* and if you use an extra file as the template for job script, the name of it should be *TorquePBS.tpl*.
2. Suggested: Function name or variable name should follow the style guide for Python code stated in `PEP 8 <https://www.python.org/dev/peps/pep-0008/>`_. In brief, both function name and variable in function should be lowercase.
3. Suggested: Two blank lines between two function block are expected.

2. Functions should be implemented in the plugin
++++++++++++++++++++++++++++++++++++++++++++++++
The plugin must provide three functions for BioQueue to call. When you develop a
new plugin, **REMEMBER TO FOLLOW THE PARAMETER LIST WE PROVIDE BELOWE!!**

submit_job
^^^^^^^^^^
This function enables BioQueue to submit a job to the cluster via
a DRM. The prototype of the function is::

  submit_job(protocol, job_id, job_step, cpu=0, mem='', vrt_mem='', queue='', log_file='', wall_time='', workspace='')

  :param protocol: string, the command a job needs to run, like "wget http://www.a.com/b.txt"
  :param job_id: int, job id in BioQueue, like 1
  :param job_step: int, step order in the protocol, like 0
  :param cpu: int, cpu cores the job will use
  :param mem: string, allocated physical memory, eg. 64G.
  :param vrt_mem: string, allocated virtual memory, eg. 64G.
  :param queue: string, job queue
  :param log_file: string, path to store the log file
  :param wall_time: string, cpu time
  :param workspace: string, the initial directory of the job, all output files should be stored in the folder, or the users will not be able to see them
  :return: int, if success, return job id in the cluster, else return 0

*Note: BioQueue will assign '' to both mem and vrt_mem if the user doesn't
define the max amount of pyhsical memory or virtual memory a job can use and
there are no prediction model to predict the amount of resource the job will
occupy.*

*Note: protocol here is just a single step in the protocol defined in BioQueue*

query_job_status
^^^^^^^^^^^^^^^^
This function provides BioQueue an interface to query the status of a job. The
prototype of the function is::

  query_job_status(job_id)

  :param job_id: int, job id in the cluster
  :return: int, job status

If the job has completed, the function should return 0. If the job is running,
it should return 1. If the job is queuing, it should return 2. If an
error occurs during the execution of a job, it should return a negative number.

cancel_job
^^^^^^^^^^
The function allows BioQueue to terminate the execution of a job. The prototype
of the function is::

  cancel_job(job_id)

  :param job_id: int, job id
  :return: if success, return 1, else return 0

3. Share the plugin with everyone
+++++++++++++++++++++++++++++++++
To share your plugin with other people, please fork BioQueue
at `github <https://github.com/liyao001/BioQueue>`_, and copy the plugins files
into ``worker>>cluster_models``. Then you can start a pull requests. Once we
receive your pull requests, we will validate it as soon as possible. After that
your plugin will be available for everyone.

Problems you may encounter with
-------------------------------

1. Install python 2.7 or high and pip without root privilege
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
Cluster users usually do not have root privilege, and the python installation
may be out-of-date. So it may be hard for biologists to configure the python
environment for BioQueue, here we provide a helper script in
*deploy/python_pip_non_root.sh*. This shell script will download source code of
Python 2.7.13 from `Python.org <https://www.python.org>`_ and compile it on the
machine. You can run the script by running::

  cd deploy
  chmod +x python_pip_non_root.sh
  ./python_pip_non_root.sh

If the compile process failed, you can download a pre-built binary
from `ActiveState <https://www.activestate.com/activepython/downloads>`_
straightforwardly. NOTICE: the pre-built binary from ActiveState cannot be used
in production environment.

After installation, you can add the bin directory to your PATH
environment variable for quicker access. For example, if you use the Bash shell
on Unix, you could place this in your ~/.bash_profile file (assuming you
installed into /home/your_name/bin)::

  export PATH=/home/your_name/bin:$PATH

then save the .bash_profile file and run::

  source ~/.bash_profile

Now you should be able to run BioQueue with the new Python.

2. Cannot run BioQueue with sqlite on clusters
++++++++++++++++++++++++++++++++++++++++++++++
*Before answer the question, we highly recommand that all users use MySQL rather
than SQLite.*
When running BioQueue on a cluster with Network File System (NFS), you may get
an error message like::

  django.db.utils.OperationalError: disk I/O error

The reason for this error is that SQLite uses reader/writer locks to control
access to the database, while those locks are unimplemented on many NFS
implementations (including recent versions of Mac OS X). So the only solution
is to use a database software like MySQL.
