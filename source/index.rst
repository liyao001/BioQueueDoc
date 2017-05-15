.. BioQueue documentation master file, created by
   sphinx-quickstart on Sat Jan 07 14:59:17 2017.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to BioQueue's documentation!
====================================
BioQueue is a lightweight and easy-to-use queue system to accelerate the proceeding of bioinformatic workflows. Based on machine learning methods, BioQueue can maximize the efficiency, and at the same time, it also reduces the possibility of errors caused by unsupervised concurrency (like memory overflow). BioQueue can both run on POSIX compatible systems (Linux, Solaris, OS X, etc.) and Windows.

How does BioQueue work?
========================
One very conspicuous characteristic of data analyses in bioinformatics is that researchers usually need to analyze large amount of data by using the same protocol and other researchers may need to use this protocol to analyze their own data too. So improving the reusability of protocol is very crucial. To achieve this goal, BioQueue explicitly differentiates two concepts. One concept is “protocol”, which is a chain of steps consisting of software and its parameters that define the behavior of the analysis. When a “protocol” is assigned with specific experimental variables, like input files, output files or sample name, the protocol will turn into a runnable “job”. So to analyze data with BioQueue, you need to create a protocol first.

BioQueue is based on a chain of checkpoints which not only provide the support for reentrancy (The ability of a program to continue its execution from where it lefts off if interrupted, without restarting from the beginning of a process), but also estimate the resources (CPU, memory and disk usage) required by following steps. And if the system resources are abundant for the next step, BioQueue will execute it, otherwise the step has to wait until there are enough resources.

Contents
========
.. toctree::
   :maxdepth: 2

   getstarted
   protocol
   job
   example
   faq
   cluster

Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
