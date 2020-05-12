---
layout: page 
title: anvi-search-functions [program]
categories: [anvio]
comments: false
image:
  featurerelative: ../../../images/header.png
  display: true
---


{% include _toc.html %}


<img src="../../images/icons/PROGRAM.png" alt="PROGRAM" style="width:100px; border:none" />

Search functions in an anvi&#39;o contigs database or genomes storage. Basically, this program searches for one or more search terms you define in functional annotations of genes in an anvi&#39;o contigs database, and generates multiple reports. The simpler report (which also is the default one) simply tells you which contigs contain genes with functions matching to serach terms you used. This file is only useful to quickly highlight matching contigs in the interface by providing it to the anvi-interactive with the `--additional-layer` parameter. You can also request a much more comprehensive report, which gives you anything you might need to know, including the matching gene caller id, functional annotation source, and full function name for each hit and serach term.

[Back to help main page](../../)

## Provides

<p style="text-align: left" markdown="1"><span style="background:#cbe4d5; padding: 0px 3px 2px 3px; border-radius: 5px;">[functions-txt](../../artifacts/functions-txt)</span></p>

## Requires or uses

<p style="text-align: left" markdown="1"><span style="background:#dcbfe8; padding: 0px 3px 2px 3px; border-radius: 5px;">[contigs-db](../../artifacts/contigs-db)</span> <span style="background:#dcbfe8; padding: 0px 3px 2px 3px; border-radius: 5px;">[genomes-storage-db](../../artifacts/genomes-storage-db)</span></p>

## Usage


{:.notice}
**No one has described the usage of this program** :/ If you would like to contribute, please see usage examples [here](https://github.com/merenlab/anvio/tree/master/anvio/docs), and feel free to add a Markdown formatted file in this directory named "anvi-search-functions.md". For a template, you can use the markdown file for `anvi-gen-contigs-database`. THANK YOU!


## Additional Resources



{:.notice}
Are you aware of resources that may help users better understand the utility of this program? Please feel free to edit [this file](https://github.com/merenlab/anvio/tree/master/bin/anvi-search-functions) on GitHub. If you are not sure how to do that, find the `__resources__` tag in [this file](https://github.com/merenlab/anvio/blob/master/bin/anvi-interactive) to see an example.
