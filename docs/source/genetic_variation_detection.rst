.. _GVD:

Detection of Genetic Variants
=============================

There are many open-source tools available that enable researchers to perform variant detection from HiC data. We aim to hightlight the tools and commands we use here at Cantata Bio call features. This is not a comprenisve list, nor do we own or manage any of the tools listed below. Please refer to their source document pages for more information or for help in trouble shooting the use of these tools. 
For each analysis performed below we provide, a link to the tool repo, the input file, the example command, and an example output for you to check your results against.

Example input files (.mcool, .hic, or bam files)
++++++++++++++++++++++++++++++++++++++++++++++++

  - Example :download:`mcool <https://s3.amazonaws.com/dovetail.pub/VariLink/mcool/K562.mcool>` file
  - Example :download:`hic <https://s3.amazonaws.com/dovetail.pub/VariLink/hic/K562.hic>` file
  - Example :download:`bam <https://s3.amazonaws.com/dovetail.pub/VariLink/bam/K562.bam>` file


Calling Structural Variants
+++++++++++++++++++++++++++

**Recommended Software**
  
  - hic_breakfinder https://github.com/dixonlab/hic_breakfinder

**Example Command**

.. code-block:: console

   	./hic_breakfinder --bam-file K562.bam --exp-file-inter inter_expect_1Mb.hg38.txt --exp-file-intra intra_expect_100kb.hg38.txt --name hic_breakfinder/K562


**Example Output(s)**

  - Example :download:`breaks file <https://s3.amazonaws.com/dovetail.pub/VariLink/hic_breakfinder/K562.breaks.txt>` file


Calling Copy Number Variation
+++++++++++++++++++++++++++++

**Recommended Software**

  - CNVkit https://cnvkit.readthedocs.io/en/stable/quickstart.html#install-cnvkit 

**Example Command**

.. code-block:: console

   # generate .cnr file
   cnvkit.py batch K562.bam -r FlatReference.cnn -p 8 -d K562
   
   # segment into copy number calls
   cnvkit.py segment K562.cnr -o K562.cns

   # generate CNV plots
   cnvkit.py scatter K562.cnr -o K562_scatter.pdf
   cnvkit.py diagram K562.cnr -o K562_diagram.pdf


**Example Output(s)**

  - Example :download:`.cnr (logR ratio) <https://s3.amazonaws.com/dovetail.pub/VariLink/cnvkit/K562.cnr>` file
  - Example :download:`.cns (segmentation) <https://s3.amazonaws.com/dovetail.pub/VariLink/cnvkit/K562.cns>` file
  - Example :download:`scatter pdf <https://s3.amazonaws.com/dovetail.pub/VariLink/cnvkit/K562_scatter.pdf>` file
  - Example :download:`diagram pdf <https://s3.amazonaws.com/dovetail.pub/VariLink/cnvkit/K562_diagram.pdf>` file


Calling SNVs and Indels
+++++++++++++++++++++++

**Recommended Software**

  - deepVariant https://github.com/google/deepvariant 

**Example Command**

.. code-block:: console

   # assumes bam file and reference are in a directory named /input
   docker run -v "in_dir":"/input" -v "out_dir:/output" google/deepvariant:"1.1.0" /opt/deepvariant/bin/run_deepvariant --model_type=WGS --ref=input/hg38.fa --reads=output/K562.bam --output_vcf=K562.vcf --intermediate_results_dir ./tmp --num_shards=8

**Example Output(s)**

  - Example :download:`VCF file <https://s3.amazonaws.com/dovetail.pub/VariLink/deepVariant/K562.vcf>` file
