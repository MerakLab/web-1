---
layout: page
title: anvi-export-state [program]
categories: [anvio]
comments: false
image:
  featurerelative: ../../../images/header.png
  display: true
---

Export an anvi&#x27;o state into a profile database.

See **[program help menu](../../../vignette#anvi-export-state)** or go back to the **[main page](../../)** of anvi'o programs and artifacts.


{% include _toc.html %}
<div id="svg" class="subnetwork"></div>
{% capture network_path %}{{ "network.json" }}{% endcapture %}
{% capture network_height %}{{ 300 }}{% endcapture %}
{% include _project-anvio-graph.html %}


## Provides

<p style="text-align: left" markdown="1"><span class="artifact-p">[state-json](../../artifacts/state-json)</span></p>

## Requires or uses

<p style="text-align: left" markdown="1"><span class="artifact-r">[pan-db](../../artifacts/pan-db)</span> <span class="artifact-r">[profile-db](../../artifacts/profile-db)</span> <span class="artifact-r">[state](../../artifacts/state)</span></p>

## Usage


This program allows you to export a <span class="artifact-n">[state](/software/anvio/help/artifacts/state)</span> from a <span class="artifact-n">[pan-db](/software/anvio/help/artifacts/pan-db)</span> or <span class="artifact-n">[profile-db](/software/anvio/help/artifacts/profile-db)</span>. The output of this is a <span class="artifact-n">[state-json](/software/anvio/help/artifacts/state-json)</span>, which you can import into another anvi'o project with <span class="artifact-n">[anvi-import-state](/software/anvio/help/programs/anvi-import-state)</span>. 

You can run this program on a <span class="artifact-n">[profile-db](/software/anvio/help/artifacts/profile-db)</span> or <span class="artifact-n">[pan-db](/software/anvio/help/artifacts/pan-db)</span> as follows: 

<div class="codeblock" markdown="1">
anvi&#45;export&#45;state &#45;s <span class="artifact&#45;n">[state](/software/anvio/help/artifacts/state)</span> \
                  &#45;p <span class="artifact&#45;n">[profile&#45;db](/software/anvio/help/artifacts/profile&#45;db)</span>  \
                  &#45;o path/to/output
</div>

To list the collections available in this database, you can run 

<div class="codeblock" markdown="1">
anvi&#45;export&#45;state &#45;p <span class="artifact&#45;n">[pan&#45;db](/software/anvio/help/artifacts/pan&#45;db)</span> \
                  &#45;&#45;list&#45;states
</div>


{:.notice}
Edit [this file](https://github.com/merenlab/anvio/tree/master/anvio/docs/programs/anvi-export-state.md) to update this information.


## Additional Resources



{:.notice}
Are you aware of resources that may help users better understand the utility of this program? Please feel free to edit [this file](https://github.com/merenlab/anvio/tree/master/bin/anvi-export-state) on GitHub. If you are not sure how to do that, find the `__resources__` tag in [this file](https://github.com/merenlab/anvio/blob/master/bin/anvi-interactive) to see an example.
