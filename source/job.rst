Job
===================
Create a Job
------------
To create a new job, you can either click the ``Create New Job`` button at the homepage or click the ``New Job`` link at the sidebar.

.. image:: https://cloud.githubusercontent.com/assets/17058337/21881028/006970dc-d8dd-11e6-88ef-e5b5e6a15c1e.png

Firstly, you need to choose a protocol for this job. To make a protocol reusable, you might have placed some wildcards in your protocol, so you need to assign values to those wildcards, like input files, sample name, etc. As mentioned before, BioQueue provides two pre-defined wildcards ``InputFile`` and ``InputFileN`` for mapping input files to a protocol. Those two wildcards are defined in ``Input files`` textbox. In BioQueue, there are three types of input files:

1. FTP file: Files uploaded through BioQueue FTP server. To make sure the safety of your data, BioQueue will hide the store path of those files. When you need to use those files, you can click the ``Choose FTP file`` button, then select files you need. Or, you can manually type ``{Uploaded:FILENAME}`` into ``Input files`` textbox.
2. Local file: For users who only use BioQueue in local environment, there's no need to transfer files through FTP. So, you can put your files at some place, and then use the absolute path of the file, like ``/mnt/biodata/RNAseq/sample1_1.fastq``.
3. Remote file: For security, BioQueue will not fetch remote files by default. So to use these files, you may need to add a download step in your protocol, like ``wget {InputFile}``.

When you want to add more than one file into ``Input files`` textbox, do not forget to add a semicolon at the end of each file. For example::

    {Uploaded:ERR030878_1.fastq.bz2};{Uploaded:ERR030878_2.fastq.bz2};

If you use ``{InputFile}`` in a step, then it will be replaced by ``user_ftp_store_path/ERR030878_1.fastq.bz2 user_ftp_store_path/ERR030878_2.fastq.bz2``, ``{InputFile1}`` will be replaced by ``user_ftp_store_path/ERR030878_1.fastq.bz2``, and ``{InputFile2}`` will be replaced by ``user_ftp_store_path/ERR030878_2.fastq.bz2``.

For user-defined wildcards, you can declare them in the ``Job parameter`` textbox by entering ``wildcard=value;``. For instance, in our demo protocol, there are two user-defined wildcards (SAMPLENAME and SEQMACHINE), if you want assign ``ERR030878`` to ``SAMPLENAME``, and ``HighSeq2000`` to ``SEQMACHINE``, enter the following text into ``Job parameter`` textbox::

    SAMPLENAME=ERR030878;SEQMACHINE=HiSeq2000;

In the end, click the ``Push job into queue`` button.

Create Batch of Jobs
--------------------
In most cases, we need to analyze multiple samples with the same protocol. And it's usually tedious to create jobs one by one. So BioQueue provides a very convenient way to do it. You can provide a file containing three columns to describe the jobs you want to create. Here are those three columns:

1. Protocol Id, which can be found in the ``Edit Protocol`` page.
2. Input files.
3. Job parameter.

Then click ``Upload and create`` button.
Note: columns should be separated by ``Tab (\t)``

Reentrancy
----------
When we manually terminate a job or some errors occur that cause the termination of a job, we might want to restart the job by directly running the step where the job was terminated rather than rerun all the steps, which is so-called reentrancy. In BioQueue, there are checkpoints before the execution of each step, so when click the ``Resume`` tag in the ``Operation`` column, the job will be automatically restart from the step where the termination occurs.

.. image:: https://cloud.githubusercontent.com/assets/17058337/21976002/9f56de7a-dc0a-11e6-884b-c6d55bc84472.png

Here is a diagram showing the difference between ``rerun`` and ``resume``.

.. image:: https://cloud.githubusercontent.com/assets/17058337/21976001/9f567c46-dc0a-11e6-83b1-519c265d6cac.png
