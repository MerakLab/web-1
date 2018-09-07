---
layout: post
title: Microbial 'Omics Workshop
modified: 2018-07-23
excerpt: "A light introduction to questions of microbial ecology and microbial omics through the story of crassphage"
comments: true
authors: [alon]
categories: [anvio]
---

{% capture images %}{{site.url}}/images/anvio/2018-07-23-microbial-omics-workshop{% endcapture %}

{% capture files %}{{site.url}}/files/2018_BPHS_WORKSHOP{% endcapture %}

{% include _toc.html %}

{% include _join-anvio-slack.html %}

{:.notice}
This workshop was developed as part of the BRI class for the University of Chicago Biophysical Sciences Graduate Program first year students in the fall quarter of 2018. The workshop was planned for 20 hours in-class, and the students were expected to do additional work on assignment outside of class hours.

The general goals of this workshop are:
1. Introduce the students to some of the microbial ecology and microbial omics type of work that is being done in the Meren Lab.
2. Learn a new practical tool (snakemake) through some hands-on experience.

The specific goals of this workshop are:
1. Get familiar with some basic questions of microbial ecology.
2. Learn how questions of microbial ecology are addressed using 'omics approahces through the example of [crassphage](https://en.wikipedia.org/wiki/CrAssphage).
3. Learn, through the example of [snakemake](https://snakemake.readthedocs.io/en/stable/), how workflow managment tools help us streamline the analysis of large collection of genomic samples.
4.  Learn how large datasets could serve as an input to machine learning algorithms, and help us address questions of microbial ecology.

## Prepare your Mac

For this workshop you are required to install the following things:

### brew

I recommend installing homebrew.
For more details on homebrew, refer to [https://brew.sh/](https://brew.sh/). There are many ways to install anvi'o, but using the `brew` command is the easiest.

### python

Your Mac already came with python, but I would recommend isntalling a different python with brew. 

I also recommend installing the [jupyter notebook](). You can do that by running this

### anvi'o (and some dependencies)

Before we install anvi'o, we need to install the following third party software:

**samtools**:
* [http://localhost:4000/2016/06/18/installing-third-party-software/#samtools](http://localhost:4000/2016/06/18/installing-third-party-software/#samtools)

**prodigal**:
* [http://localhost:4000/2016/06/18/installing-third-party-software/#prodigal](http://localhost:4000/2016/06/18/installing-third-party-software/#prodigal)

**HMMER**:
* [http://localhost:4000/2016/06/18/installing-third-party-software/#hmmer](http://localhost:4000/2016/06/18/installing-third-party-software/#hmmer)

**GSL**:
* [http://localhost:4000/2016/06/18/installing-third-party-software/#gnu-scientific-library](http://localhost:4000/2016/06/18/installing-third-party-software/#gnu-scientific-library)

**Numpy**:
* [http://localhost:4000/2016/06/18/installing-third-party-software/#numpy](http://localhost:4000/2016/06/18/installing-third-party-software/#numpy)

**Cython**:
* [http://localhost:4000/2016/06/18/installing-third-party-software/#cython](http://localhost:4000/2016/06/18/installing-third-party-software/#cython)

**After we have all of the above, we can install anvi'o**:
* [http://localhost:4000/2016/06/26/installation-v2/#painless-installation-with-homebrew](http://localhost:4000/2016/06/26/installation-v2/#painless-installation-with-homebrew)

## Introduction to microbial ecology

The introduction will include a lecture by Dr. Eren. Slides will be available [here]() as soon as possible.

## Crassphage

Slides for the introduction to crassphage and the workshop plan could be downloaded [here](https://github.com/ShaiberAlon/2018_microbial_omics_workshop/raw/master/Presentations/Crassphage_presentation.pptx).

The background includes review of these crassphage related papers:

[![crassphage_paper]({{images}}/crassphage_paper.png)]( https://www.nature.com/articles/ncomms5498){:.center-img .width-60}

[![Yutin_et_al]({{images}}/Yutin_et_al.png)]( https://www.nature.com/articles/s41564-017-0053-y){:.center-img .width-60}

[![Shkoporov_et_al]({{images}}/Shkoporov_et_al.png)]( https://www.biorxiv.org/content/early/2018/06/26/354837){:.center-img .width-60}


## Using a mapping approach to asses the occurence of crassphage in metagenomes

Following a similar approach to the one in Dutilh et al., we will use read recruitment to quantify the occurence of crassphage in gut metagenomes.

We will use metagenomes from the following studies: [The Human Microbiome Project Consortium 2012](https://www.nature.com/articles/nature11234) (USA), [Rampelli et al. 2015](https://www.sciencedirect.com/science/article/pii/S0960982215005370) (Tanzania), [Qin et al. 2012](https://www.nature.com/articles/nature11450) (China), and [Brito et al. 2016](https://www.nature.com/articles/nature18927) (Fiji). This will allow us to get some minimal understanding of the global distribution of crassphge.

{:.notice}
In this workshop we will use a total of 481 metagenomes, in this section of the workshop we will only analyze a small portion of these metagenomes. The purpose is to understand what are the steps that are required for this analysis, and to understand that to do each step manually is not a good idea. Hence, later, we will use a snakefile to analyze the rest of the samples. Notice that I directly shared files with the students, so if you are not a student of the workshop, but interested in getting these files, please contact me.

<div class="extra-info" markdown="1">
<span class="extra-info-header">Assignment - check for crassphage in samples</span>

I provided you with 8 bam files. Each student will pick two samples, and go through the following steps in order to view these bam files with anvio. To keep things simple, I provide you with bam files, and hence, you are spared from taking many of the analysis steps (e.g. quality filtering of the metagenomes, mapping of short reads to reference genome, formatting of mapping results, etc.)

**generate a contigs database**

This step proccesses the reference fasta file that we are using. In this case, it is the genome of crassphage (which could be downloaded [here](https://www.ncbi.nlm.nih.gov/nuccore/674660337?report=fasta)). You can learn more by reffering to the help menu or the [this section of the anvio metagenomics tutorial](http://merenlab.org/2016/06/22/anvio-tutorial-v2/#creating-an-anvio-contigs-database). To generate the database run:

```bash
anvi-gen-contigs-database -f crassphage.fasta -c crassphage-contigs.db -n crassphage
```

**profile the bam file**

The bam file contains the alignment information for the short reads against the reference genome. `anvi-profile` computes variouos stats from the alignment, and paves the way to the visualization of this information. This step is performed for each sample separately. For more information refer to the help menu and to [this section of the anvio metagenomics tutorial](http://merenlab.org/2016/06/22/anvio-tutorial-v2/#anvi-profile).

To run `anvi-profile` for a bam file:

```bash
anvi-profile -i PATH/TO/BAM_FILE.bam -c crassphage-contigs.db
```

**merge the bam files**

After all the individual profiling steps are done, we merge these profiles together so that we can visualize them. For more information refer to the help menu, or to [this section of the anvio metagenomics tutorial](http://merenlab.org/2016/06/22/anvio-tutorial-v2/#anvi-merge).

```bash
anvi-merge */PROFILE.db -o SAMPLES-MERGED -c crassphage-contigs.db --skip-concoct-binning
```

Notice that I added the flag `--skip-concoct-binning`, since we are not interested in binning in this case. If you don't know what binning is, that's ok, it's not important for this workshop.

After these steps are done. use `anvi-interactive` to view the results. Do you see any trend in the global distribution of crassphage?

In class, we will also use the `--gene-mode` of anvi-interactive to look at the occurence of the crassphage genes.

We will also use the `inspect` option of the interface to look at SNVs.
</div>

## Using snakemake to run the analysis steps

Since bioinformatics tends to include many steps of analysis, it is becoming more and more popular to use workflow management tools to perform analysis. Two popular tools are [nextflow](https://www.nextflow.io) and [snakemake](http://snakemake.readthedocs.io/en/stable/index.html). Here we will get familiar with snakemake and write a simple workflow to run the steps from above.

We will go through this introduction to snakemake from Johannes Koester (the developer of snakemake). But the best way to become familiar with snakemake is to refer to the [online docummentation](http://snakemake.readthedocs.io/en/stable/index.html).

<div class="extra-info" markdown="1">
<span class="extra-info-header">Assignment - write a snakefile to excecute the same steps from earlier</span>

We will write a snakefile that accepts a list of bam-files with the path to each bam file, and a name for each sample. We will then use this snakefile to run these steps for all the 481 samples from the global studies.
</div>

## Predicting a host for crassphage

Similar to the approach in Dutilh et al., we will use the abundance of crassphage in metagenomes along with the abundance of various taxons, to try to predict a host for crassphage. In order to asses the occurence of various bacteria in our collection of metagenomes, I ran [krakenHLL](https://www.biorxiv.org/content/early/2018/06/06/262956). And the results for the species and genus levels are provided in [samples-species-taxonomy.txt]({{files}}/samples-species-taxonomy.txt), and [samples-genus-taxonomy.txt]({{files}}/samples-genus-taxonomy.txt), respectively. We will now examine two alternative approaches to predicting a host for crassphage. Both of these approaches rely on the idea that the host of crassphage should have a positively correlating occurence with crassphage. A [jupyter notebook](http://jupyter.org/) with all the steps for this part of the workshop is available [here]({{files}}/BPHS_WORKSHOP_RF.ipynb)

### Loading the data

```python
import pandas as pd

tax_level = 'species'
data_file = 'samples-'+ tax_level + '-taxonomy.txt'
target_file = 'crassphage-samples-information.txt'

raw_data = pd.read_csv(data_file, sep='\t', index_col=0)
raw_target = pd.read_csv(target_file, sep='\t', index_col=0)
```

But before we try to predict anything from the data, we will pre-process the data.

### Pre-processing the data

First we remove all the samples that don't have crassphage (crassphage negative samples).


```python
all_samples = raw_data.index
crassphage_positive_samples = raw_target.index[raw_target['presence']==True]
print('The number of samples in which crassphage was detected: %s' % len(crassphage_positive_samples))
crassphage_negative_samples = raw_target.index[raw_target['presence']==False]
print("The number of samples in which crassphage wasn't detected: %s" % len(crassphage_negative_samples))
# Keep only positive samples
samples = crassphage_positive_samples
```

    The number of samples in which crassphage was detected: 80
    The number of samples in which crassphage wasn't detected: 240


Ok, so there a lot more negative samples than positive samples.

Let's look at where are the positive samples coming from.


```python
for o in ['USA', 'CHI', 'FIJ', 'TAN']:
    a = len([s for s in crassphage_positive_samples if s.startswith(o)])
    b = len([s for s in all_samples if s.startswith(o)])
    c = a/b * 100
    print('The number of crassphage positive samples from %s is %s out of total %s (%0.f%%)' % (o, a, b, c))
```

    The number of crassphage positive samples from USA is 53 out of total 103 (51%)
    The number of crassphage positive samples from CHI is 16 out of total 46 (35%)
    The number of crassphage positive samples from FIJ is 11 out of total 167 (7%)
    The number of crassphage positive samples from TAN is 0 out of total 8 (0%)


We can see that crassphage is most prevalent in the US cohort, but also quite common in the Chinese cohort. Whereas it is nearly absent from the Fiji cohort, and completely absent from the eight hunter gatherers from Tanzania.

#### Normalizing the data
The krakenhll output is in counts, so we will normalize this output to portion values, so that the sum of all taxons in a sample will be 1.
We will also normalize the crassphage values, but since we don't have the needed information to translate these values to portion of the microbial community, we will just make sure that the values are between zero and one, by deviding by the largest values.


```python
normalized_data = raw_data.div(raw_data.sum(axis=1), axis=0)
normalized_target = raw_target['non_outlier_mean_coverage']/raw_target['non_outlier_mean_coverage'].max()
```

#### Removing rare taxons
We will remove the taxons that don't occur in at least 3 of the crassphage positive samples. The reasoning here is that a taxon that occurs so uncommonly is more likely to be an outcome of spuriuous misclassification, and would anyway contribute very little to a successful classifier.


```python
occurence_in_positive_samples = normalized_data.loc[crassphage_positive_samples,]
qualifying_taxons = normalized_data.columns[(occurence_in_positive_samples>0).sum()>3]
portion = len(qualifying_taxons)/len(normalized_data.columns) * 100
print('The number of qualifying taxons is %s out of total %s (%0.f%%)' % (len(qualifying_taxons), \
                                                                         len(normalized_data.columns), \
                                                                         portion))

data = normalized_data.loc[samples, qualifying_taxons]
target = normalized_target[samples]
```

    The number of qualifying taxons is 69 out of total 273 (25%)


### A clustering approach to identifying a host
We will try a hierarchical clustering approach similar to what was done in the crassphage paper.

First we will merge the data together (i.e. add the crassphage coverage as a column to the kraken table):


```python
# add the crassphage column
merged_df = pd.concat([data, target], axis=1)
# Changing the name of the crassphage column
merged_df.columns = list(merged_df.columns[:-1]) + ['crassphage']
# Write as TAB-delimited

# anvi'o doesn't like charachters that are not alphanumeric or '_'
# so we will fix index and column labels
import re
f = lambda x: x if x.isalnum() else '_'
merged_df.columns = [re.sub('\_+', '_', ''.join([f(ch) for ch in s])) for s in list(merged_df.columns)]
merged_df.index = [re.sub('\_+', '_', ''.join([f(ch) for ch in s])) for s in list(merged_df.index)]

# Save data to a TAB-delimited file
merged_df.to_csv(tax_level + '-matrix.txt', sep='\t', index_label = 'sample')
```

Now we leave the python notebook and go back to the command line.

There are a few steps needed to be done in order to visualize this data with anvio (described [here](http://merenlab.org/tutorials/interactive-interface/)), but we will use a [snakefile]({{files}}/snakemake_to_generate_manual_interactive_interface_for_tabular_data.snake) I wrote to streamline thie process. This snakefile requires a config file, which is available [here]({{files}}/config.json).

{:.notice}
If you try to use this snakefile on your own data, beware that this snakefile is not highly robust, and might not work for your case of interest.

```bash
snakemake -s snakemake_to_generate_manual_interactive_interface_for_tabular_data.snake --configfile config.json
```

I also created a slightly nicer view of the data which we could import to the manual profile database (the state file is available [here]({{files}}/crassphage-host-predict-default-state.json):

```bash
anvi-import-state -p 00_ANVIO_FIGURE/PROFILE.db \
                  -s crassphage-host-predict-default-state.json \
                  -n default
```

I also made some selections in the interface to highlight the potential hosts. Let's load the collection I created (available here: [collection file]({{files}}/crassphage-host-predict-default-collection.txt), and [collection info file]({{files}}/crassphage-host-predict-default-collection-info.txt))

```bash
anvi-import-collection -p 00_ANVIO_FIGURE/PROFILE.db \
                       -C default \
                       --bins-info crassphage-host-predict-default-collection-info.txt \
                       crassphage-host-predict-default-collection.txt
```

Now we can view the results:

```bash
anvi-interactive --manual \
                 -d species-matrix.txt-transposed-item-names-fixed \
                 -p 00_ANVIO_FIGURE/PROFILE.db \
                 -t 00_ANVIO_FIGURE/crassphage_species.newick \
                 --title crassphage_species
```

This is it what it looks like:

[![host_clustering]({{images}}/host_clustering.png)]( {{images}}/host_clustering.png)){:.center-img .width-60}

We can see that this includes members and close relatives of the *Bacteroides* genus, hence we get a similar result to Dutilh et al.

### Training a random forest regression model

Similar to the clustering approach, we will use each one of the taxonomy tables to train a Random Forrest regression model, and see if we can predict possible hosts for crassphage.
In preparing this part of the tutorial, I advised [this blog post](https://www.blopig.com/blog/2017/07/using-random-forests-in-python-with-scikit-learn/) by Fergus Boyles.

We now train a Random Forrest regression model on the clean data. The input data for the model are the normalized abundances from KrakenHLL, and the target data for prediction is the normalized non-outlier mean coverage of crassphage.


```python
from sklearn import ensemble
n_trees = 64
RF = ensemble.RandomForestRegressor(n_trees, \
                                    oob_score=True, \
                                    random_state=1)

RF.fit(data,target)
```

    RandomForestRegressor(bootstrap=True, criterion='mse', max_depth=None,
               max_features='auto', max_leaf_nodes=None,
               min_impurity_decrease=0.0, min_impurity_split=None,
               min_samples_leaf=1, min_samples_split=2,
               min_weight_fraction_leaf=0.0, n_estimators=64, n_jobs=1,
               oob_score=True, random_state=1, verbose=0, warm_start=False)

### Model Results

The model finished, let's check the oob value (a kind of R^2 estimation for the model. For more details refer to [this part of the scikit-learn docummentation](http://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestRegressor.html#sklearn.ensemble.RandomForestRegressor.score)).


```python
RF.oob_score_
```

    -0.029949172312407013

This doesn't look great (to understand why, you can refer to [this question on stats.stackexchange.com](https://stats.stackexchange.com/questions/133406/is-a-negative-oob-score-possible-with-scikit-learns-randomforestregressor). In essence, this means that the model has no prediction power. Despite this, let's look at the importance of the features. We will take the 10 most important features according to the model, and plot the abundance of crassphage against the abundance of each of the most important taxa:


```python
import numpy as np
features_according_to_order_of_importance = data.columns[np.argsort(-RF.feature_importances_)]


from matplotlib import pyplot as plt
g = features_according_to_order_of_importance[0:10]
for f in g:
    plt.figure()
    plt.scatter(data.loc[data[f].sort_values().index,f], \
                target.loc[data[f].sort_values().index,'non_outlier_mean_coverage'])
    plt.title(f)
    plt.show()
```

[![output_14_0]({{images}}/jupyter/output_14_0.png)]( {{images}}/jupyter/output_14_0.png){:.center-img .width-40}

[![output_14_1]({{images}}/jupyter/output_14_1.png)]( {{images}}/jupyter/output_14_1.png){:.center-img .width-40}

[![output_14_2]({{images}}/jupyter/output_14_2.png)]( {{images}}/jupyter/output_14_2.png){:.center-img .width-40}

[![output_14_3]({{images}}/jupyter/output_14_3.png)]( {{images}}/jupyter/output_14_3.png){:.center-img .width-40}

[![output_14_4]({{images}}/jupyter/output_14_4.png)]( {{images}}/jupyter/output_14_4.png){:.center-img .width-40}

[![output_14_5]({{images}}/jupyter/output_14_5.png)]( {{images}}/jupyter/output_14_5.png){:.center-img .width-40}

[![output_14_6]({{images}}/jupyter/output_14_6.png)]( {{images}}/jupyter/output_14_6.png){:.center-img .width-40}

[![output_14_7]({{images}}/jupyter/output_14_7.png)]( {{images}}/jupyter/output_14_7.png){:.center-img .width-40}

[![output_14_8]({{images}}/jupyter/output_14_8.png)]( {{images}}/jupyter/output_14_8.png){:.center-img .width-40}

[![output_14_9]({{images}}/jupyter/output_14_9.png)]( {{images}}/jupyter/output_14_9.png){:.center-img .width-40}


We can see that this list is dominated by members and close relatives of the _Bacteroides_ genus, which is encouraging. In addition, a few other things are included, but we need to remember that in a regression model things that anti-correlate could also count as important features.