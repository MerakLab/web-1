---
layout: page
title: anvi-get-split-coverages [program]
categories: [anvio]
comments: false
image:
  featurerelative: ../../../images/header.png
  display: true
---

Export splits and the coverage table from database.

See **[program help menu](../../../vignette#anvi-get-split-coverages)** or go back to the **[main page](../../)** of anvi'o programs and artifacts.


{% include _toc.html %}
<div id="svg" class="subnetwork"></div>
{% capture network_path %}{{ "network.json" }}{% endcapture %}
{% capture network_height %}{{ 300 }}{% endcapture %}
{% include _project-anvio-graph.html %}


## Provides

<p style="text-align: left" markdown="1"><span class="artifact-p">[coverages-txt](../../artifacts/coverages-txt)</span></p>

## Requires or uses

<p style="text-align: left" markdown="1"><span class="artifact-r">[profile-db](../../artifacts/profile-db)</span> <span class="artifact-r">[contigs-db](../../artifacts/contigs-db)</span> <span class="artifact-r">[collection](../../artifacts/collection)</span> <span class="artifact-r">[bin](../../artifacts/bin)</span></p>

## Usage


This program returns the nucleotide-level coverage data for a specific set of the splits in your <span class="artifact-n">[profile-db](/software/anvio/help/artifacts/profile-db)</span>. 

If you want to get the coverage data for all splits in your <span class="artifact-n">[profile-db](/software/anvio/help/artifacts/profile-db)</span>, run <span class="artifact-n">[anvi-export-splits-and-coverages](/software/anvio/help/programs/anvi-export-splits-and-coverages)</span> with the flag `--splits-mode`. 

Simply provide a <span class="artifact-n">[profile-db](/software/anvio/help/artifacts/profile-db)</span> and <span class="artifact-n">[contigs-db](/software/anvio/help/artifacts/contigs-db)</span> pair and specific which splits you want to look at. You have two oftens to do this: 

1.  Provide a single split name. (You can list all splits available with `--list-splits`)

<div class="codeblock" markdown="1">
anvi&#45;get&#45;split&#45;coverages &#45;p <span class="artifact&#45;n">[profile&#45;db](/software/anvio/help/artifacts/profile&#45;db)</span> \
                         &#45;c <span class="artifact&#45;n">[contigs&#45;db](/software/anvio/help/artifacts/contigs&#45;db)</span> \
                         &#45;o <span class="artifact&#45;n">[coverages&#45;txt](/software/anvio/help/artifacts/coverages&#45;txt)</span> \ 
                         &#45;&#45;split&#45;name Day17a_QCcontig9_split_00003
</div>


2. Provide both the name of a <span class="artifact-n">[bin](/software/anvio/help/artifacts/bin)</span> and the <span class="artifact-n">[collection](/software/anvio/help/artifacts/collection)</span> it is contained in. 

<div class="codeblock" markdown="1">
anvi&#45;get&#45;split&#45;coverages &#45;p <span class="artifact&#45;n">[profile&#45;db](/software/anvio/help/artifacts/profile&#45;db)</span> \
                         &#45;c <span class="artifact&#45;n">[contigs&#45;db](/software/anvio/help/artifacts/contigs&#45;db)</span> \
                         &#45;o <span class="artifact&#45;n">[coverages&#45;txt](/software/anvio/help/artifacts/coverages&#45;txt)</span> \ 
                         &#45;b <span class="artifact&#45;n">[bin](/software/anvio/help/artifacts/bin)</span> \
                         &#45;C <span class="artifact&#45;n">[collection](/software/anvio/help/artifacts/collection)</span>
</div>

You can list all collections available with `--list-collections` or all bins in a collection with `--list-bins`. Alternatively, you could run <span class="artifact-n">[anvi-show-collections-and-bins](/software/anvio/help/programs/anvi-show-collections-and-bins)</span> on your <span class="artifact-n">[profile-db](/software/anvio/help/artifacts/profile-db)</span> to get a more comprehensive overview. 


{:.notice}
Edit [this file](https://github.com/merenlab/anvio/tree/master/anvio/docs/programs/anvi-get-split-coverages.md) to update this information.


## Additional Resources


* [Using this program to generate split coverage visualizations across samples](http://merenlab.org/2019/11/25/visualizing-coverages/#visualize-only-the-coverage-of-a-split-across-samples)


{:.notice}
Are you aware of resources that may help users better understand the utility of this program? Please feel free to edit [this file](https://github.com/merenlab/anvio/tree/master/bin/anvi-get-split-coverages) on GitHub. If you are not sure how to do that, find the `__resources__` tag in [this file](https://github.com/merenlab/anvio/blob/master/bin/anvi-interactive) to see an example.
