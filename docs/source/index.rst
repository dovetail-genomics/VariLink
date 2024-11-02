.. VariLink documentation master file, created by
   sphinx-quickstart on Fri Jan 22 15:26:09 2021.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.
.. image:: /images/DOV_FINAL_LOGO_2017_RGB.svg
   :width: 100pt


Welcome to VariLink documentation
=================================


.. image:: /images/VariLink_intro1.png
   :alt: VariLink_Intro1


Overview
========
- Dovetail™ LinkPrep® assay is designed for the rapid generation of genomic interaction data specifically designed for the detection of genetic variants

.. image:: /images/workflow.png
   :alt: VariLink_Intro2

- Key benefits of LinkPrep:

  - Compressed overall workflow to single day compared to 2-5 day workflow of other Hi-C approaches
  - Flexible input requirements
  - Improved mapping rate and even genomic coverage result in more complete contact matrices enabling variant detection similar to shotgun libraries

.. image:: /images/histograms.png
   :alt: evenCoverage
  
- This guide will take you step by step on how to process and QC your LinkPrep library, how to interpret the QC results, how to generate contact maps, and how to perform genetic variant detection. If you don’t yet have a sequencing LinkPrep library and you want to get familiar with the data, you can download LinkPrep sequence libraries from our publicly available data sets. 

- The QC process starts with aligning the reads to a reference genome then retaining high quality mapped reads. From there the mapped data will be used to generate a pairs file with pairtools, which categorizes pairs by read type and insert distance, this step both flags and removes PCR duplicates. Once pairs are categorized, counts of each class are summed and reported.

- If this is your first time following this tutorial, please check the :ref:`Before you begin page <BYB>` first.


.. toctree::
   :maxdepth: 2
   :caption: Contents:

   before_you_begin
   
   fastq_to_bam
   
   library_qc

   contact_map

   genetic_variation_detection
   
   data_sets

   support


Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
