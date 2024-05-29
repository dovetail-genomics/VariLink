.. _BYB:

Before you begin
================

Have a copy of the VariLink scripts on your machine:
----------------------------------------------------

Clone this repository:

.. code-block:: console

   git clone https://github.com/dovetail-genomics/VariLink.git


Dependencies
------------

Make sure that the following dependencies are installed:

- `pysam <https://pysam.readthedocs.io/en/latest/>`_
- `tabulate <https://pypi.org/project/tabulate/>`_
- `bedtools <https://bedtools.readthedocs.io/en/latest/index.html>`_
- `deeptools <https://deeptools.readthedocs.io/en/develop/>`_
- `matplotlib <https://matplotlib.org/>`_
- `pandas <https://pandas.pydata.org/pandas-docs/stable/dsintro.html>`_
- `bwa <https://github.com/lh3/bwa>`_
- `pairtools <https://github.com/open2c/pairtools>`_
- `samtools <https://github.com/samtools/samtools>`_
- `preseq <https://github.com/smithlabcode/preseq>`_
- `hic_breakfinder <https://github.com/dixonlab/hic_breakfinder>`_
- `cnvkit <https://cnvkit.readthedocs.io/en/stable/quickstart.html#install-cnvkit>`_
- `docker <https://docs.docker.com/engine/install>`_

If you are facing any issues with the installation of any of the dependencies, please contact the supporter of the relevant package.

Input files
-----------

For this tutorial you will need: 

* **fastq files** R1 and R2, either fastq or fastq.gz are acceptable
* **reference in a fasta file format**, e.g. hg38

If you don't already have your own input files or want to run a test on a small data set, you can download sample fastq files from the :ref:`VariLink Data Sets section<DATASETS>`. The 2M data set is suitable for a quick testing of the instruction in this tutorial. 

.. code-block:: console

   wget https://s3.amazonaws.com/dovetail.pub/VariLink/fastqs/K562.1x.R1.fastq.gz
   wget https://s3.amazonaws.com/dovetail.pub/VariLink/fastqs/K562.1x.R2.fastq.gz

Use the following command to download a copy of hg38 with the non-canonical chromosomes removed

.. code-block:: console

   wget https://s3.amazonaws.com/dovetail.pub/reference/human/hg38.fa

For Structural Variant (SV) detection, you will need a copy of the inter- and intra-chromosomal interaction frequency background models

.. code-block:: console

   wget https://s3.amazonaws.com/dovetail.pub/VariLink/hic_breakfinder/inter_expect_1Mb.hg38.txt
   wget https://s3.amazonaws.com/dovetail.pub/VariLink/hic_breakfinder/intra_expect_100kb.hg38.txt

Pre-alignment
-------------

For downstream steps you will need a genome file, genome file is a tab delimited file with chromosome names and their respective sizes. If you don't already have a genome file follow these steps:

1. Generate an index file for your reference, a reference file with only the main chromosomes should be used (e.g. without alternative or unplaced chromosomes).

**Command:**

.. code-block:: console

   samtools faidx <ref.fa>


**Example:**

.. code-block:: console

   samtools faidx hg38.fa

Faidx will index the ref file and create <ref.fasta>.fai on the reference directory.

.. _GENOME:

2. Use the index file to generate the genome file by printing the first two columns into a new file.

**Command:**

.. code-block:: console

   cut -f1,2 <ref.fa.fai> > <ref.genome>


**Example:**

.. code-block:: console

   cut -f1,2 hg38.fa.fai > hg38.genome


In line with the 4DN project guidelines and from our own experience optimal alignment results are obtained with Burrows-Wheeler Aligner (bwa).
Prior to alignment, generate a bwa index file for the chosen reference.


.. code-block:: console

   bwa index <ref.fasta>


**Example:**

.. code-block:: console

   bwa index hg38.fa



No need to specify an output path, the bwa index files are automatically generated at the reference directory. Please note that this step is time consuming, however you need to run it only once for a reference. 

To avoid memory issues, some of the steps require writing temporary files into a temp folder, please generate a temp folder and remember its full path. Temp files may take up to x3 of the space that the fastq.gz files are taking, that is, if the total volume of the fastq files is 5Gb, make sure that the temp folder can store at least 15Gb.

**Command:**

.. code-block:: console

   mkdir <full_path/to/tmpdir>


**Example:**

.. code-block:: console

   mkdir /home/ubuntu/ebs/temp


In this example the folder `temp` will be generated on a mounted volume called `ebs` on a user account `ubuntu`.