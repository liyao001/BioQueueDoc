Protocol
===================
Like wet experiments, a protocol (or workflow, pipeline in other software) in bioinformatics is built from several linked *steps* that consume inputs, process and convert data and produce results. To analyze data, you need to create a protocol which contains executable steps to tell BioQueue how to analyze it first.

Create a Protocol
-----------------

To create a new protocol, you can either click ``Create Protocol`` button at the homepage or click ``New Protocol`` at the sidebar. Since we would like to keep a balance between simplicity and flexibility at the same time, BioQueue possesses a shell command like syntax. Basically, each step of the protocol consists of software and its parameter. For example, if you want to use HISAT2 to map next-generation sequencing reads, the original command may like::

    hisat2 -x ucsc_hg19.fastq -1 reads_1.fastq -2 reads_2.fastq -S alns.sam -t 16

In BioQueue, you need to enter the software ("hisat2") into ``Software`` textbox, and then enter "-x ucsc_hg19.fastq -1 reads_1.fastq -2 reads_2.fastq -S alns.sam -t 16" into ``Parameter`` textbox.

.. image:: https://cloud.githubusercontent.com/assets/17058337/21838123/37a7925e-d80b-11e6-8dc1-1176965870f0.png

Actually, a typical protocol contains many steps, so you can click the ``Add Step`` button to add more step. After you added all steps, click ``Create Protocol`` button.

Edit Steps
-------------
When you need to change parameters of a certain step, you should open ``Edit Protocol`` at the sidebar. Then move mouse to ``Operation`` column where the protocol locates in, and click the ``Check Steps`` label. When the steps' table shows up, you can click the parameter of the step. Now you can edit the parameter. Once you click any place at that page, your changes will be saved automatically.

.. image:: https://cloud.githubusercontent.com/assets/17058337/21838124/37ac2e18-d80b-11e6-837c-23c8e7d178d8.png

Make Protocol Reusable
----------------------
We usually need to use the same protocol to analyze dozens of samples. So, in order to make protocol reusable, we implemented a simple tag system. Users can use tags to replace variables like input file name, output file name, etc. In BioQueue, any words enclosed by braces will be considered as a tag, like {ThreadN}. For example, if you want to utilize HISAT2 to map reads, you can replace reads_1.fastq with {READS1}, and replace reads_2.fastq with {READS2}. And then you can specify those two tags when you create a job by entering::

	READS1=Sample1_1.fastq;READS2=Sample1_2.fastq

into ``Job Parameter`` textbox.

.. image:: https://cloud.githubusercontent.com/assets/17058337/21838122/3780a626-d80b-11e6-911d-8e8b59124a68.png

If you want to analyze Sample 2, just change Sample1_1.fastq and Sample1_2.fastq to Sample2_1.fastq and Sample2_2.fastq, and the rest can be done in the same manner::

	READS1=Sample2_1.fastq;READS2=Sample2_2.fastq

Below is a table of pre-defined tags you can use them directly in your parameters:

+---------------+------------------------------------------------------------------------------+
|Tag            |Description                                                                   |
+===============+==============================================================================+
|InputFile      |The initial input files which maps to files you provide when you create a job.|
+---------------+------------------------------------------------------------------------------+
|InputFileN     |The n-th file in input files.                                                 |
+---------------+------------------------------------------------------------------------------+
|LastOutput     |Output files of last step.                                                    |
+---------------+------------------------------------------------------------------------------+
|LastOutputN    |The n-th output file of last step (in alphabetical order).                    |
+---------------+------------------------------------------------------------------------------+
|OutputN-M      |The m-th output file of the n-th step (in alphabetical order)+.               |
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

Use Reference
-------------
Reference in BioQueue is used to store information to be referenced in users' protocols. So, it's similar to "variable" in programming. We recommend to create references for genome files, SNP annotation files and gene annotation files, etc. Let's suppose you have a reference genome file in folder '/mnt/biodata/hg38.fa', so you can type "hg38" in ``Reference Name`` textbox and assign the value "/mnt/biodata/hg38.fa" into the ``Reference Value`` textbox.

.. image:: https://cloud.githubusercontent.com/assets/17058337/21838125/37c77d4e-d80b-11e6-8a3f-795ec896a824.png

Technically, a reference is a user-defined tag. So, don't forget to add braces before you use a reference in any of your protocol, like {HG38}.

Share Protocol With Peer
------------------------
To share a protocol with a peer, you need to open ``Edit protocol`` page, and choose ``Share`` in the ``Operation`` column.

.. image:: https://cloud.githubusercontent.com/assets/17058337/21994280/e9fc3bce-dc59-11e6-8cbe-a0b9407f65a9.png

Then enter username of the peer you want to share with, and click ``Share with a peer``.

.. image:: https://cloud.githubusercontent.com/assets/17058337/21994281/e9fd1af8-dc59-11e6-940f-710997f114ee.png

Examples
--------
Calling variants in RNAseq (STAR-gatk)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
This protocol is created according to gatk's Best-Practices provided by Broad Institute::

	STAR --genomeDir {STAR_HG38} --readFilesIn {READS1} {READS2} --runThreadN {ThreadN}
	STAR --runMode genomeGenerate --genomeDir 2pass --genomeFastaFiles {HG38} --sjdbFileChrStartEnd SJ.out.tab --sjdbOverhang 75 --runThreadN {ThreadN}
	STAR --genomeDir 2pass --readFilesIn {READS1} {READS2} --runThreadN {ThreadN}
	java -jar {picard} AddOrReplaceReadGroups I=Aligned.out.sam O=rg_added_sorted.bam SO=coordinate RGID=id RGLB=library RGPL=platform RGPU={SEQMACHINE} RGSM={SAMPLENAME}
	java -jar {picard} MarkDuplicates I=rg_added_sorted.bam O=dedupped.bam  CREATE_INDEX=true VALIDATION_STRINGENCY=SILENT M=output.metrics
	java -jar {gatk} -T SplitNCigarReads -R {HG38} -I dedupped.bam -o split.bam -rf ReassignOneMappingQuality -RMQF 255 -RMQT 60 -U ALLOW_N_CIGAR_READS
	java -jar {gatk} -T HaplotypeCaller -R {HG38} -I input.bam -dontUseSoftClippedBases -stand_call_conf 20.0 -stand_emit_conf 20.0 -o output.vcf
	java -jar {gatk} -T VariantFiltration -R {HG38} -V output.vcf -window 35 -cluster 3 -filterName FS -filter "FS > 30.0" -filterName QD -filter "QD < 2.0" -o output.hard.filtered.vcf

Reference settings:

+--------------+------------------------------------------------------------------------------------------------------------+
|Reference Name|Reference Value                                                                                             |
+==============+============================================================================================================+
|STAR_HG38     |The folder stores hg38 genome index for star, this is generated by command ``STAR --runMode genomeGenerate``|
+--------------+------------------------------------------------------------------------------------------------------------+
|HG38          |Path of a reference genome file, like ``/mnt/biodata/hg38.fa``                                              |
+--------------+------------------------------------------------------------------------------------------------------------+
|picard        |Path of picard.jar, like ``/mnt/biosoftware/picard.ja``                                                     |
+--------------+------------------------------------------------------------------------------------------------------------+
|gatk          |Path of GenomeAnalysisTK.jar, like ``/mnt/biosoftware/GenomeAnalysisTK.jar``                                |
+--------------+------------------------------------------------------------------------------------------------------------+
