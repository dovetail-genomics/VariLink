.. _LQ:

Alignment and Proximity-Ligation QC
===================================

At step :ref:`Removing PCR duplicates<DUPs>` you used the flag `--output-stats`, generating a stats file in addition to the pairsam output (e.g. --output-stats stats.txt). The stats file is an extensive output of pairs statistics as calculated by pairtools, including total reads, total mapped, total dups, total pairs for each pair of chromosomes etc'. Although you can use directly the pairtools stats file as is to get informed on the quality of the VariLink library, we find it easier to focus on a few key metrics. We include in this repository the script ``get_qc.py`` that summarize the paired-tools stats file and present them in percentage values in addition to absolute values.

**Command:**

.. code-block:: console

   python3 ./VariLink/get_qc.py -p <stats.txt>


**Example:**

.. code-block:: console

   python3 ./VariLink/get_qc.py -p stats.txt 


After the script completes, it will print:

.. code-block:: console

   Total Read Pairs                              1,765,718  100%
   Unmapped Read Pairs                           0          0.0%
   Mapped Read Pairs                             1,765,644  100.0%
   PCR Dup Read Pairs                            0          0.0%
   No-Dup Read Pairs                             1,765,644  100.0%
   No-Dup Cis Read Pairs                         1,418,245  80.32%
   No-Dup Trans Read Pairs                       347,399    19.68%
   No-Dup Valid Read Pairs (cis >= 1kb + trans)  1,322,938  74.93%
   No-Dup Cis Read Pairs < 1kb                   442,706    25.07%
   No-Dup Cis Read Pairs >= 1kb                  975,539    55.25%
   No-Dup Cis Read Pairs >= 10kb                 796,075    45.09%


Complexity
----------

If you performed a shallow sequencing experiment (e.g. < 10 Million reads) and running a QC analysis to decide which library to use for deep sequencing (DS), it is recommended to evaluate the complexity of the library before moving to DS. 

The `lc_extrap` utility of the `preseq` package aims to predict the complexity of sequencing libraries. 


``preseq`` options:


.. csv-table::
   :file: tables/preseq.csv
   :header-rows: 1
   :widths: 20 20 60
   :class: tight-table

Please note that the input bam file should be a version prior to dups removal.

``preseq lc_extrap`` command example for extrapolating library complexity:

**Command:**

.. code-block:: console

  preseq lc_extrap -bam -pe -extrap 2.1e9 -step 1e8 -seg_len 1000000000 -output <output file> <input bam file>


**Example:**

.. code-block:: console

   preseq lc_extrap -bam -pe -extrap 2.1e9 -step 1e8 -seg_len 1000000000 -output out.preseq mapped.PT.bam


In this example the output file out.preseq will detail the extrapolated complexity curve of your library, with the number of reads in the first column and the expected distinct read value in the second column. For a typical experiment (human sample) check the expected complexity at 400M reads (to show the content of the file, type cat out.preseq). Expected unique pairs at 400M sequencing is at least ~ 125 million

.. image:: /images/3.Complexity.png


QC Assessment
-------------

- Pass/No Pass Metrics

  - Mapping rate % - did it align well
  - Duplicate rate % - is the library complex enough
  - Cis >1kb - does the library contain long-range information
  - Mean coverage - is there enough coverage

- Pass/No Pass Values 

  - The table below summarizes the minimum passing values for the metrics defined above.

+--------------------------------+--------------------------------------+
| Metric                         | Value                                |
+================================+======================================+
| Mapping rate                   | > 50%                                |
+--------------------------------+--------------------------------------+
| Duplicate rate                 | < 50%                                |
+--------------------------------+--------------------------------------+
| % Cis contacts > 1kb           | > 20%                                |
+--------------------------------+--------------------------------------+
| Mean Coverage                  | See sequencing recommendations below | 
+--------------------------------+--------------------------------------+

Sequencing Recommendations
--------------------------

- VariLink was designed to enable flexibility around sequence depth as different classes of variants require differing sequence depth to confidently call. For large structural variants such as translocations or gains and losses >1mb, only 5-10X genomic coverage is required. For SNV and INDELs, a more WGS application, we recommend similar sequencing depths as shotgun libraries which is 30-40X genomic coverage. Also keep in mind that tumor purity can play a role in how sequencing depth can impact detection capabilities

+---------------------------+---------------------------------------------+
| Feature                   | Sequencing Depth    | Tumor Purity          |
+===========================+=====================+=======================+
| SVs & CNVs Only           | 50-100M read pairs  | Samples > 20% purity  |
+---------------------------+---------------------+-----------------------+
| SVs, CNVs, INDELs, & SNVs | 300-400M read pairs | Samples > 50% purity* |
+---------------------------+---------------------+-----------------------+

* for samples with lower tumor purity - additional library and more sequencing is recommended
