.. _LQ:

Alignment and Proximity-Ligation QC
===================================

At step :ref:`Removing PCR duplicates<DUPs>` you used the flag `--output-stats`, generating a stats file in addition to the pairsam output (e.g. --output-stats stats.txt). The stats file is an extensive output of pairs statistics as calculated by pairtools, including total reads, total mapped, total dups, total pairs for each pair of chromosomes etc'. Although you can use directly the pairtools stats file as is to get informed on the quality of the VariLink library, we find it easier to focus on a few key metrics. We include in this repository the script ``get_qc.py`` that summarize the paired-tools stats file and present them in percentage values in addition to absolute values.

The images below explains how the values on the QC report are calculated:

.. image:: /images/1.Aligning.png

.. image:: /images/2.Cis_trans_valids.png

**Command:**

.. code-block:: console

   python3 ./VariLink/get_qc.py -p <stats.txt>


**Example:**

.. code-block:: console

   python3 ./VariLink/get_qc.py -p stats.txt 


After the script completes, it will print:

.. code-block:: console

   Total Read Pairs                              10,000,000     100%
   Unmapped Read Pairs                           325,358       3.25%
   Mapped Read Pairs                             8,129,270    81.29%
   PCR Dup Read Pairs                            218,657       2.82%
   No-Dup Read Pairs                             7,847,613    78.48%
   No-Dup Cis Read Pairs                         5,616,943    71.58%
   No-Dup Trans Read Pairs                       2,230,670    28.42%
   No-Dup Valid Read Pairs (cis >= 1kb + trans)  5,929,881    75.56%
   No-Dup Cis Read Pairs < 1kb                   1,917,732    24.44%
   No-Dup Cis Read Pairs >= 1kb                  3,699,211    47.14%
   No-Dup Cis Read Pairs >= 10kb                 3,221,239    41.05%



Complexity
----------

If you performed a shallow sequencing experiment (e.g. 10M reads) and running a QC analysis to decide which library to use for deep sequencing (DS), it is recommended to evaluate the complexity of the library before moving to DS. 

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

  - No-Dup Cis Read Pairs >= 1kb – This value demonstrates that the proximity-ligation step was successful, and the majority of the data are useful in downstream analyses (e.g. loop calling).
  - For Shallow QC Sequencing Complexity at 400M Read Pairs – This value informs how many unique reads a library can support.
  - For Deep - sequencing No-Dup Read Pairs

- Pass/No Pass Values 

  - The table below summarizes the minimum passing values for the metrics defined above. The cut-off values were determined for both shallow sequenced (10 million read pairs 2 x 150 bp) and deep sequenced data (200-300 Million read pairs 2 x 150 bp).

+--------------------------------+-----------------------------+----------------------------------------------+
| Metric                         | Shallow Sequencing          | Deep Sequencing                              |
+================================+=============================+==============================================+
| No-Dup Cis Read Pairs >= 1kb   | >40% of no-dup read pairs   | >40% of no-dup read pairs                    |
+--------------------------------+-----------------------------+----------------------------------------------+
| Complexity @ 400M Read Pairs   | >125 million                | NA                                           |
+--------------------------------+-----------------------------+----------------------------------------------+
| No-Dup Read Pairs              | NA                          | >125 million                                 |
+--------------------------------+-----------------------------+----------------------------------------------+

Sequencing Recommendations
--------------------------

VariLink was designed to support looping calling with one sample. This requires generating four libraries from a single proximity-ligation reaction. This does not mean you need to sequence all four libraries. The amount of sequencing and the number of libraries you need to to sequence is dependent on the feature you are trying to detect and the resolution (or bin size) you wish to call features at. The table below outlines the number of libraries, total sequencing depth in read pairs, and how many read pairs are needed per library, and finally the minimal amount of no-dup read pairs summed across the libraries for each feature at given resolutions:

+------------------+--------------+-------------------+--------------------+--------------------------------+--------------------------------------------------------+
| Feature          | Resolution   | Total # libraries | Total # read pairs | Total # read pairs per library | Minimal # of no-dup read pairs summed across libraries |
+==================+==============+===================+====================+================================+========================================================+
| A/B Compartments | 50-100 kb    | 1                 | 200 Million        | 200 Million                    | >80 Million                                            |
+------------------+--------------+-------------------+--------------------+--------------------------------+--------------------------------------------------------+
| TADS             | 25 kb        | 2                 | 400 Million        | 200 Million                    | >150 Million                                           |
+------------------+--------------+-------------------+--------------------+--------------------------------+--------------------------------------------------------+
|                  | 10 kb        | 2                 | 600 Million        | 300 Million                    | >300 Million                                           |
+------------------+--------------+-------------------+--------------------+--------------------------------+--------------------------------------------------------+
|                  | 5 kb         | 4                 | 800 Million        | 200 Million                    | >400 Million                                           |
+------------------+--------------+-------------------+--------------------+--------------------------------+--------------------------------------------------------+
| Loops            | 10 kb        | 4                 | 800 Million        | 200 Million                    | >400 Million                                           |
+------------------+--------------+-------------------+--------------------+--------------------------------+--------------------------------------------------------+
|                  | 5 kb         | 4                 | 1200 Million       | 300 Million                    | >500 Million                                           |
+------------------+--------------+-------------------+--------------------+--------------------------------+--------------------------------------------------------+

To generate the most complete matrix you can from a single 500 thousand cell input, you need sequence 4 libraries to a total of 1200 million read pairs (300 million per library).
