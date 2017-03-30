Protocol
===================
Like wet experiments, a protocol (or workflow, pipeline in other software) in bioinformatics is built from several linked steps that consume inputs, process and convert data and produce results. To analyze data, you need to create a protocol to tell BioQueue what it should do for you.

Create a Protocol
-----------------

To create a new protocol, you can either click ``Create Protocol`` button at the homepage or click ``New Protocol`` at the sidebar. Since neither programming nor software engineering is included in a typical biological curriculum, making the syntax as easy as possible is very necessary. When creating a protocol, BioQueue implements the shell command-like syntax rather than a domain-specific language (DSL). Basically, each step of the protocol consists of software and its parameter (which can easily get from the software’s documentation). For example, if you want to use HISAT2 to map reads produced by next-generation sequencing, the original command may like this::

    hisat2 -x ucsc_hg19.fastq -1 Sample1_1.fastq -2 Sample1_2.fastq -S alns.sam -t 16

In BioQueue, you need to enter the software ("hisat2") into ``Software`` textbox, and then enter "-x ucsc_hg19.fastq -1 reads_1.fastq -2 reads_2.fastq -S alns.sam -t 16" into ``Parameter`` textbox.

.. image:: https://cloud.githubusercontent.com/assets/17058337/21838123/37a7925e-d80b-11e6-8dc1-1176965870f0.png

Actually, a typical protocol contains many steps, so you can click the ``Add Step`` button to add more step. After you added all steps, click ``Create Protocol`` button.

Make Protocol Reusable
----------------------
Though BioQueue explicitly separates the concepts of “protocol” and “job”, a protocol can convert into a runnable job without assigning any experimental values. However, we strongly recommend you replace those values with wildcards (strings embraced with braces, like “{ThreadN}”) to make the protocol reusable and reproducible. For instance, if you want to utilize HISAT2 to map reads, you can replace “Sample1_1.fastq” with {READS1}, and replace “Sample1_2.fastq” with {READS2}. So, the protocol may like this::

	hisat2 -x ucsc_hg19.fastq -1 {READS1} -2 {READS2} -S alns.sam -t 16

And then you should tell BioQueue the value of the two wildcards when you create a job by entering::

  READS1=Sample1_1.fastq;READS2=Sample1_2.fastq;

into ``Job Parameter`` textbox.

.. image:: https://cloud.githubusercontent.com/assets/17058337/24161724/2a205cd0-0ea0-11e7-9165-7272f2eee1bf.png

Note: BioQueue will automatically load all the experimental variables into the ``Job parameter`` textbox.

And if you want to analyze one more sample (for example Samples), you just need to type::

	READS1=Sample2_1.fastq;READS2=Sample2_2.fastq

Otherwise, you will have to create a new protocol.

Mapping Files Between Steps
^^^^^^^^^^^^^^^^^^^^^^^^^^^
In most cases, a step denotes how to create output files from input files. Since a protocol usually consists of many steps, making mapping of files flexible enough is a very important issue. So BioQueue provides three types of file mapping methods to do this.

The first method is to write the file name directly. For some tools, the output files have standard names. One example is `STAR <https://github.com/alexdobin/STAR>`_, when it finished mapping RNA-SEQ reads, it would produce those files:

1. Log.out
2. Log.progress.out
3. Log.final.out
4. Aligned.out.sam
5. SJ.out.tab

So, if you want to use the mapped SAM file in the following steps, you can enter “Aligned.out.sam” straightforwardly.

The second method is to use the **“Suffix”** wildcard. For example, if you want to use the SAM file produced by the previous step, you can enter *“{Suffix:sam}”*. If you would like to use the **“Suffix”** wildcard to map file from any steps before rather than the last step, **“Suffix:N-FileSuffix”** might be very helpful (“N” means the n-th steps).

The third method is to use the **“Output”** family wildcards. Here is a table of those wildcards:

+---------------+------------------------------------------------------------------------------+
|Wildcard       |Description                                                                   |
+===============+==============================================================================+
|InputFile      |The initial input files which maps to files you provide when you create a job.|
+---------------+------------------------------------------------------------------------------+
|InputFile:N    |The n-th file in input files.                                                 |
+---------------+------------------------------------------------------------------------------+
|LastOutput     |Output files of last step.                                                    |
+---------------+------------------------------------------------------------------------------+
|LastOutput:N   |The n-th output file of last step (in alphabetical order).                    |
+---------------+------------------------------------------------------------------------------+
|Output:N-M     |The m-th output file of the n-th step (in alphabetical order)+.               |
+---------------+------------------------------------------------------------------------------+
|AllOutputBefore|All output files before this step.                                            |
+---------------+------------------------------------------------------------------------------+

More About Wildcards
^^^^^^^^^^^^^^^^^^^^
There are two main types of wildcards in BioQueue: pre-defined wildcards and user-defined wildcards (experimental variables and reference). Below is a table of pre-defined wildcards:

+---------------+------------------------------------------------------------------------------+
|Wildcard       |Description                                                                   |
+===============+==============================================================================+
|Suffix:X       |Output file(s) with a "X" suffix in the last step.                            |
+---------------+------------------------------------------------------------------------------+
|Suffix:N-X     |Output file(s) with a "X" suffix in the n-th step.                            |
+---------------+------------------------------------------------------------------------------+
|Suffix:N-X-M   |The m-th output file with a "X" suffix in the n-th step.                      |
+---------------+------------------------------------------------------------------------------+
|InputFile      |The initial input files which maps to files you provide when you create a job.|
+---------------+------------------------------------------------------------------------------+
|InputFile:N    |The n-th file in input files.                                                 |
+---------------+------------------------------------------------------------------------------+
|LastOutput     |Output files of last step.                                                    |
+---------------+------------------------------------------------------------------------------+
|LastOutput:N   |The n-th output file of last step (in alphabetical order).                    |
+---------------+------------------------------------------------------------------------------+
|Output:N-M     |The m-th output file of the n-th step (in alphabetical order).                |
+---------------+------------------------------------------------------------------------------+
|History:N-X    |Output file "X" in the n-th job.                                              |
+---------------+------------------------------------------------------------------------------+
|AllOutputBefore|All output files before this step.                                            |
+---------------+------------------------------------------------------------------------------+
|Job            |Job ID.                                                                       |
+---------------+------------------------------------------------------------------------------+
|Workspace      |Local storage path for the job.                                               |
+---------------+------------------------------------------------------------------------------+
|ThreadN        |The number of CPUs in the running node.                                       |
+---------------+------------------------------------------------------------------------------+

BioQueue provides a auto complete function when you employee those pre-defined tags:

.. image:: https://cloud.githubusercontent.com/assets/17058337/21838121/377dfb2e-d80b-11e6-860b-9ca319a73fe2.gif

Now, let’s have a look at the user-defined wildcards. As we mentioned before, BioQueue suggests users use wildcards to denote experimental variables in protocols, like sample name. This type of user-defined wildcards need to be assigned as ``Job parameter`` when creating jobs. In bioinformatics, there are some data that can be cited in different protocols, such as reference genome, GENCODE annotation, etc. So, in BioQueue, biological data that may be used in multiple protocols is called a “reference”. This is the other type of user-defined wildcards and it is defined at the ``Reference`` page.

.. image:: https://cloud.githubusercontent.com/assets/17058337/21838125/37c77d4e-d80b-11e6-8a3f-795ec896a824.png

Use Reference
+++++++++++++
The aim of implementing the concept of “reference” is to reduce the redundancy of protocols (see the table below), consequently, references are available to all protocols of the user. We recommend creating references for genome files, SNP annotation files and gene annotation files, etc. Let's suppose you have a human reference genome file “hg38.fa” in the folder “/mnt/biodata/reference_genome”, thus you can type "HG38" in ``Reference Name`` textbox and assign the value "/mnt/biodata/reference_genome/hg38.fa" into the ``Reference Value`` textbox.

Here is a table showing how the usage of reference can reduce the redundancy of protocols.

+----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+
|                      |Without Reference                                                                                                                                                                 |With Reference                                                                                                                                     |
+======================+==================================================================================================================================================================================+===================================================================================================================================================+
|One step in protocol A|hisat2-build ``/mnt/biodata/reference_genome/hg38.fa`` genome                                                                                                                     |hisat2-build ``{HG38}`` genome                                                                                                                     |
+----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+
|One step in protocol B|java -jar gatk.jar -T HaplotypeCaller -R ``/mnt/biodata/reference_genome/hg38.fa`` -I input.bam -dontUseSoftClippedBases -stand_call_conf 20.0 -stand_emit_conf 20.0 -o output.vcf|java -jar gatk.jar -T HaplotypeCaller -R ``{HG38}`` -I input.bam -dontUseSoftClippedBases -stand_call_conf 20.0 -stand_emit_conf 20.0 -o output.vcf|
+----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+

Note: Don't forget to add braces before you use a reference in any of your protocol, like ``{HG38}``!

Edit Steps
-------------
When you need to change parameters of a certain step, you should click ``Edit Protocol`` at the sidebar. Then you move mouse to ``Operation`` column where the protocol locates in, and click the ``Check Steps`` label.

.. image:: https://cloud.githubusercontent.com/assets/17058337/21838124/37ac2e18-d80b-11e6-837c-23c8e7d178d8.png

When the steps' table shows up, you can click the parameter of the step. Now you can edit the parameter. Once you click any place at that page, your changes will be saved automatically.



Share Protocol With Peer
------------------------
We know the importance of making computational analysis in life sciences:

1. Easily to get started for researchers who do not have a strong background in computer science (accessibility);
2. Easily to reproduce the experimental results;

So, protocols written by BioQueue can be shared with a peer who are using the same platform, and BioQueue can generate a portable protocol file which can be published on the Internet.

To share a protocol with a peer, you need to open the ``Edit protocol`` page, and choose ``Share`` in the ``Operation`` column.

.. image:: https://cloud.githubusercontent.com/assets/17058337/21994280/e9fc3bce-dc59-11e6-8cbe-a0b9407f65a9.png

Then enter username of the peer you want to share with, and click ``Share with a peer``.

.. image:: https://cloud.githubusercontent.com/assets/17058337/21994281/e9fd1af8-dc59-11e6-940f-710997f114ee.png

To share a protocol with the public, you need to open the same dialog, and click the ``Build a sharable protocol`` button, then a protocol file would be generated. You can publish this protocol on `BioQueue Open Platform <http://open.bioqueue.org>`_ or any other web forums.
