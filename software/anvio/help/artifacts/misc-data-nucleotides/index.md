---
layout: page
title: misc-data-nucleotides [artifact]
categories: [anvio]
comments: false
image:
  featurerelative: ../../../images/header.png
  display: true
---


{% include _toc.html %}


<img src="../../images/icons/CONCEPT.png" alt="CONCEPT" style="width:100px; border:none" />

A CONCEPT-type anvi'o artifact. This artifact is typically generated, used, and/or exported **by anvi'o** (and not provided by the user)..

Back to the **[main page](../../)** of anvi'o programs and artifacts.

## Provided by


<p style="text-align: left" markdown="1"><span class="artifact-p">[anvi-import-misc-data](../../programs/anvi-import-misc-data)</span></p>


## Required or used by

<p style="text-align: left" markdown="1"><span class="artifact-r">[anvi-delete-misc-data](../../programs/anvi-delete-misc-data)</span> <span class="artifact-r">[anvi-export-misc-data](../../programs/anvi-export-misc-data)</span></p>

## Description

This is a section of your <span class="artifact-n">[contigs-db](/software/anvio/help/artifacts/contigs-db)</span> that contains custom additional information about specific nucleotides. 

Take a look at [this blogpost](http://merenlab.org/2020/07/22/interacdome/#6-storing-the-per-residue-binding-frequencies-into-the-contigs-database) for potential uses in the interactome (which will likely be added to anvi'o in v7) and the motivation behind this program. 

Similarly to other types of miscellaneous data (like <span class="artifact-n">[misc-data-items](/software/anvio/help/artifacts/misc-data-items)</span>), this information can be populated into a <span class="artifact-n">[contigs-db](/software/anvio/help/artifacts/contigs-db)</span> with <span class="artifact-n">[anvi-import-misc-data](/software/anvio/help/programs/anvi-import-misc-data)</span> and can be either numerical or categorical. 

For example, this information could describe specific nucleotides that are known to be SNVs from another experiment, various key nucleotides for binding to ligands, or positions known to have other modifications (such as m1A or s4U).


{:.notice}
Edit [this file](https://github.com/merenlab/anvio/tree/master/anvio/docs/artifacts/misc-data-nucleotides.md) to update this information.

