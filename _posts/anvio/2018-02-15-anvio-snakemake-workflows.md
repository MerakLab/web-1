---
layout: post
title: Anvi'o snakemake workflows
modified: 2018-02-15
excerpt: "Bringing the magic of anvi'o together with the wonders of snakemake."
comments: true
authors: [alon]
categories: [anvio]
---

{% capture images %}{{site.url}}/images/anvio/2018-02-15-anvio-snakemake-workflows{% endcapture %}

{% include _toc.html %}

{:.notice}
This post will only work for anvi'o `v?` or later, AND snakemake `v4` or later.

[Snakemake](https://snakemake.readthedocs.io/en/stable/) is a very useful and robust language for creating
computational workflows. Recently we have been using it extensivley and it has been wonderful.

In order to let others enjoy anvi'o together with the wonders of snakemake, we embarked on an effort to make
"easy-to-use" (well, not *too* easy .. after all this is 'science') snakefiles for some of the commonly used
workflows involving anvi'o.

The purpose of this post is to describe two things:

* How to use snakemake workflows with anvi'o (for all users)

* A proposed architecture of how these workflows should be written (for developers and power users)


# How to use anvi'o snakemake workflows

Running workflows (such as pangenomics or assembly-based metagenomics) requires many steps, especially for a project
with numerous samples. The snakemake workflows are here to help you streamline the repetative steps. To learn more
about snakemake, please refer to the snakemake docummentation, [here](https://snakemake.readthedocs.io/en/stable/),
I will just mention that each step of the analysis (for example running `anvi-gen-contigs-databse`) corresponds to a
"rule" in the workflow.

## Introducing: `anvi-run-workflow`

In order to make it easier to use these workflows for anvi'o users who are less familiar with snakemake,
we added a new program, `anvi-run-workflow`.

```
$ anvi-run-workflow -h

WARNING
===============================================
If you publish results from this workflow, please do not forget to cite
snakemake (doi:10.1038/nmeth.3176)

usage: anvi-run-workflow [-h] [-w WORKFLOW]
                         [--get-default-config OUTPUT_FILENAME]
                         [--list-workflows] [--list-dependencies]
                         [-c CONFIG_FILE] [--dry-run] [--save-workflow-graph]
                         [-A ...]

optional arguments:
  -h, --help            show this help message and exit

ESSENTIAL INPUTS:
  Things you must provide or this won't work

  -w WORKFLOW, --workflow WORKFLOW
                        You must specify a workflow name. To see a list of
                        available workflows run --list-workflows.

ADDITIONAL STUFF:
  additional stuff

  --get-default-config OUTPUT_FILENAME
                        Store a json formatted config file with all the
                        default settings of the workflow. This is a good draft
                        you could use in order to write your own config file.
                        This config file contains all parameters that could be
                        configured for this workflow. NOTICE: the config file
                        is provided with default values only for parameters
                        that are set by us in the workflow. The values for the
                        rest of the parameters are determined by the relevant
                        program.
  --list-workflows      Print a list of available snakemake workflows
  --list-dependencies   Print a list of the dependencies of this workflow. You
                        must provide a workflow name and a config file.
                        snakemake will figure out which rules need to be run
                        according to your config file, and according to the
                        files available on your disk. According to the rules
                        that need to be run, we will let you know which
                        programs are going to be used, so that you can make
                        sure you have all of them installed and loaded.
  -c CONFIG_FILE, --config-file CONFIG_FILE
                        TBD
  --dry-run             Don't do anything real. Test everything, and stop
                        right before wherever the developer said 'well, this
                        is enough testing', and decided to print out results.
  --save-workflow-graph
                        Save a graph representation of the workflow. If you
                        are using this flag and if your system is unable to
                        generate such graph outputs, you will hear anvi'o
                        complaining (still, totally worth trying).
  -A ..., --additional-params ...
                        Additional snakemake parameters to add when running
                        snakemake. NOTICE: --additional-params HAS TO BE THE
                        LAST ARGUMENT THAT IS PASSED TO anvi-run-workflow,
                        ANYTHING THAT FOLLOWS WILL BE CONSIDERED AS PART OF
                        THE ADDITIONAL PARAMETERS THAT ARE PASSED TO
                        SNAKEMAKE. Any parameter that is accepted by snakemake
                        should be fair game here, but it is your
                        responsibility to make sure that whatever you added
                        makes sense. To see what parameters are available
                        please refer to the snakemake documentation. For
                        example, you could use this to set up cluster
                        submission using --additional-params --cluster "YOUR-
                        CLUSTER-SUBMISSION-CMD"
```

This program is meant to help you prepare the config file (which we will promptly learn about) and then run
the workflow.

If you want to see which workflows are available run:

```
$ anvi-run-workflow --list-workflows
Available workflows ..........................: contigs, metagenomics, pangenomics
```

We will do our best to keep this page up-to-date when we add or change things.

## The config.json file

In order to allow flexibility, the user can configure many parameters for the workflow using a config file.
Even if you want to use all the default parameters you still have to supply a config file, but don't worry, we got you covered,
you can always start by running:

```
anvi-run-workflow -w WORKFLOW-NAME --get-default-config OUTPUT_FILENAME
```

This will generate a config file with all the parameters that could be configured. For each parameter that you don't plan
to change, you can just keep it as is, or even remove it from your config file (to make your config file shorter and cleaner).

The config file contains configurations of three types:

1. general parameters of the workflow (e.g. a name for you project, which mode of the workflow to use (see metagenomics
workflow, `reference_mode`), etc.).

2. rule specific parameters (e.g. `min_contig_length` for `anvi-profile`). We tried as much as possible to allow the user to
change any parameter that is configurable in the underlying software that are used in the workflow.

3. names for the output directories of the workflows.

<div class="extra-info" markdown="1">

<span class="extra-info-header">A note regarding rule specific parameters</span>
To make things consistent, we decided that the way that parameters appear in the config file
will be identicle to how to appear in the underlying program. If there are multiple ways to use a argument
then we chose the longer one. For example, while `anvi-run-hmms` allows you to configure the path to the
HMM profiles directory using either `-H` or `--hmm-profile-dir`, we only allow you to use `--hmm-profile-dir` in the config file. But don't worry, if you use the wrong name, there will be an helpful message to let you know 
what are the correct arguments you can use.
</div>

Below you can find example config files for each one of the workflows.

# Metagenomics workflow

The majority of the steps used in this pipeline are extensively described in the
[anvi'o user tutorial for metagenomic workflow](http://merenlab.org/2016/06/22/anvio-tutorial-v2/).
However, in contrast to that tuturial which starts with a FASTA files of contigs and BAM files, this
pipeline includes steps to get there, including quality filtering, assembly, and mapping steps. detailed below.

The default entering point to this pipeline are the unprocessed raw reads from one or more metagenomes.
The default output of the pipline is an anvi'o merged profile database ready for refinement of bins
(or whatever it is that you want to do with it).

The pipline includes the following steps:

1. QC of the metagenomes using [illumina-utils](https://github.com/merenlab/illumina-utils/). Including
generating a report of the results of the QC.
2. (Co-)Assembly of metagenomes using either [megahit](https://github.com/voutcn/megahit) or [idba_ud](https://github.com/loneknightpy/idba).
3. Generating an anvi'o CONTIGS database using [anvi-gen-contigs-database](http://merenlab.org/2016/06/22/anvio-tutorial-v2/#anvi-gen-contigs-database).
4. Mapping short reads from metagenomes to the contigs using [bowtie2](http://bowtie-bio.sourceforge.net/bowtie2/index.shtml).
5. Profiling individual BAM files using [anvi-profile](http://merenlab.org/2016/06/22/anvio-tutorial-v2/#anvi-profile).
6. Merging resulting anvi'o profile databases using [anvi-merge](http://merenlab.org/2016/06/22/anvio-tutorial-v2/#anvi-merge).

This pipeline also includes all steps that are included in the `contigs` workflow which is described below. In short,
these are all the steps to annotate your contigs database(s) with functions, HMMs, and taxonomy.

## Setting the stage

TBD (basically instructions to download a tar with all the files needed for this tutorial).

All the files that are currently needed are here: `/users/ashaiber/sandbox/snakemake_tutorial/metagenomics/WORKFLOW_TUTORIAL_DATA.tar.gz`. I just didn't get the chance to write a nice `wget`, etc. lines for it.

## Standard usage


As mentioned above, the standard usage of this workflow is meant to go through all the steps from raw reads to having a merged profile database (or databases) that are ready for binning.

All you need is a bunch of FASTQ files, and a `samples.txt` file. Here, we will go through a mock example with three small metagenomes. These metagenomes were made by choosing a small number of reads from three [HMP](https://www.hmpdacc.org/) metagenomes. In your working directory you have the following `samples.txt` file, let's have a look:

```bash
$ cat samples.txt

sample	group	r1	r2
sample_01	G01	three_samples_example/sample-01-R1.fastq.gz	three_samples_example/sample-01-R2.fastq.gz
sample_02	G02	three_samples_example/sample-02-R1.fastq.gz	three_samples_example/sample-02-R2.fastq.gz
sample_03	G02	three_samples_example/sample-03-R1.fastq.gz	three_samples_example/sample-03-R2.fastq.gz
```

There are four columns:

sample - a name that you chose to give to each one of your metagenomic samples.

group - Often, when we want to bin genomes from metagenomic assemblies we wish to do so by co-assembling multiple samples  (see for example [Albertsen et al. 2012](https://www.nature.com/articles/nbt.2579) or [this blog post that explains how we binned the TARA Oceans metagenomes](http://merenlab.org/data/2017_Delmont_et_al_HBDs/)). The purpose of this column is to define which samples are going to be co-assembled together. This is an optional column, if this column is not included in the `samples_txt` file, then each sample will be assembled separately. By default, only the samples that were used for the co-assembly would then be mapped to the resulting assembly. If you want, you can co-assemble groups of samples, but then map **all** samples to each assembly (see `all_against_all` option for the config file).

r1, r2 - these two columns hold the path (could be either a relative or an absolute path) to the fastq files that correspnd to the sample. 

<div class="extra-info" markdown="1">

<span class="extra-info-header">Merging pair-end fastq files</span>

If multiple pair-end reads fastq files correspond to the same samlpe, they could be listed separated by a comma (with no space). This could be relevant, for example, if one sample was sequenced in multiple runs. Let's take our `samples_txt` from above, but now assume that `sample_01` was sequenced twice. The `samples_txt` file would then look like this:

```
sample	group	r1	r2
sample_01	G01	three_samples_example/sample-01a-R1.fastq.gz,three_samples_example/sample-01b-R1.fastq.gz	three_samples_example/sample-01a-R2.fastq.gz,three_samples_example/sample-01b-R1.fastq.gz
sample_02	G02	three_samples_example/sample-02-R1.fastq.gz	three_samples_example/sample-02-R2.fastq.gz
sample_03	G02	three_samples_example/sample-03-R1.fastq.gz	three_samples_example/sample-03-R2.fastq.gz
```

**Notice:** if your fastq files were already QC-ied, and you didn't do it with this workflow, and you wish to skip QC, **AND** from some reason you didn't already merge the fastq files that should be merged together, then you must do so manually, and then provide only one `r1` file and one `r2` file per sample. 
</div>


The defalt name for your `samples_txt` file is `samples.txt`, but you can use a different name by specifying it in the config file (see below).

In your working directory there is a config file `config-idba_ud.json`, let's take a look at it.

```
{
    "samples_txt": "samples.txt",
    "idba_ud": {
        "--min_contig": 1000,
        "threads": 11,
        "run": true
    }
}
```

Very short. Every configurable parameter (and there are a lot of them. We tried to keep things flexible) that is not mentioned here will be assigned a default value. 

<div class="extra-info" markdown="1">

<span class="extra-info-header">Getting a default config</span>
If you wish to see all the configurable parameters and their default values run:

```
anvi-run-workflow -w metagenomics \
                  --get-default-config NAME-FOR-YOUR-DEFAULT-CONFIG.json
```

We usually like to start a default config and then delete every line for which we wish to keep the default (if you don't delete it, then nothing would happen, but why keep garbage in your files?).
</div>

So what do we have in the our example config file above?

`samples_txt` - A path for our `samples_txt` (frankly, since we used the default name `samples.txt` then we didn't really have to include this).

`idba_ud` - a few parameters for `idba_ud`. 

 -	`run` - Currently two assembly software are available in the workflow: megahit and idba_ud. We didn't set neither of these as the default software, and hence if you wish to assemble things then you must set the `run` parameter to `true` for one (and only one) if these. 
	
 - `--min-contig` - from the help menu of `idba_ud` we learn:
[![idba_ud_min_contig]({{images}}/idba_ud_min_contig.png)]( {{images}}/idba_ud_min_contig.png){:.center-img .width-50}
idab_ud has the default as 200, and we want it as 1,000, and hence we include this in the config.

 - `threads` - when you wish to use multi-threads you can specify how many threads to use for each step using this parameter. Here we chose 11.

<div class="extra-info" markdown="1">

<span class="extra-info-header">A note on rule specific parameters</span>
We suggest that you take a minute to look at the default config file. run:

```bash
anvi-run-workflow -w metagenomics --get-default-config default-metagenomics-config.json
```

It is very big, and that's why we didn't paste it here. Like we said, we tried keeping things flexible, and that means having many parameters available for you.

But there are some general things you can notice:

 - **threads** - every rule has the parameter "threads" available to it. This is meant for the case in which you are using multi-threads to run things. To learn more about how snakemake utilizes threads you can refer to the snakemake documentation. We decided to allow to set the number of threads for all rules, including ones that we ourselves never use more than 1 (why? because, why not? maybe you would one day need it for some reason. Don't judge). When **threads** is the only parameter that is available for a rule, it means that there is nothing else that you could configure for this rule. Specifically, it means you don't even get to choose whether this rule is ran or not. But don't worry, snakemake would make sure that steps that are not necessary will not run.
 
 - **run** - some rules have this parameter. The rules that have this parameter are optional rules. To make sure that an optional rule is ran you need to set the `run` parameter to `true`. IF you wish an optional rule not to run, then you must set `run` to `false` or simply an empty string (`""`). Some of the optional rules run by default and others don't. You can find out what is the default behaviour by looking at the default config file. As mentioned above, if a rule doesn't have the **run** parameter it means that snakemake will infer whether it needs to run or not (just have some trust please!).

 - **parameters with an empty value (`""`)** - Many of the parameters in the default config file get an empty value. This means that the default parameter that is provided by the underlying program will be used. For example, the rule `anvi_gen_contigs_database` is responsible for running `anvi-gen-contigs-database` (we tried giving intuitive names for rules :-)), which has the parameter `--split-length`. In the default config file will see the configurations:

Meren, I need your help in formatting here. Thank you. basically I want the json below, and the text that follows it to be idented like the text above.

```
    "anvi_gen_contigs_database": {
        "--project-name": "{group}",
        "threads": 5,
        "--description": "",
        "--skip-gene-calling": "",
        "--external-gene-calls": "",
        "--ignore-internal-stop-codons": "",
        "--skip-mindful-splitting": "",
        "--contigs-fasta": "",
        "--split-length": "",
        "--kmer-size": ""
    },
```

By refering to the help menu of `anvi-gen-contigs-database` you will find that the default for `--split-length` is 20,000.
You may notice another interesting thing,which is that the value for `--project-name` is `"{group}"`. This is a little magic trick to make it so that the project name in your contigs database would indentical to the group name that you supplied in the config file. If you wish to understand this syntax, you may read about [the snakemake wildcards](http://snakemake.readthedocs.io/en/stable/snakefiles/rules.html#wildcards).


</div>


Ok, so now we have everything we need to start, let's first run a sanity check and create a workflow graph for our workflow:

```
anvi-run-workflow -w metagenomics -c config-idba_ud.json --save-workflow-graph
```

A file named `workflow.png` was created and should look like this:

[![idba_ud_workflow1]({{images}}/idba_ud_workflow1.png)]( {{images}}/idba_ud_workflow1.png){:.center-img .width-50}

<div class="extra-info" markdown="1">

<span class="extra-info-header">Using dot on MAC OSX</span>

Generating the workflow graph requires the usage of [dot](https://en.wikipedia.org/wiki/DOT_(graph_description_language)).
If you are using MAC OSX, you can use [dot](https://en.wikipedia.org/wiki/DOT_(graph_description_language)) by installing [graphviz](http://www.graphviz.org/), simply run `brew install graphviz`.
</div>

Take a minute to take a look at this image to understand what is going on. From a first look it might seem complicated, but it is fairly straight forward (and also, shouldn't you know what is going on with your data?!?).

If you wish to run this on a cluster (like I am), then you want to be familiar with snakemake's [`--cluster`](http://snakemake.readthedocs.io/en/stable/snakefiles/rules.html#job-properties) command. In order to do this we must get familiar with the `--additional-params` option of `anvi-run-workflow`. The purpose of `--additional-params` is to allow you to access any configuration that is available through snakemake (i.e. anything that is listed when you look at the help menu of snakemake through `snakemake -h` is fair game as an input for `--additional-params`). For example you can do,

``` bash
anvi-run-workflow -w metagenomics \
                            -c config-idba_ud.json \
                            --additional-params \
                            --notemp \
                            --ignore-incomplete
```

in order to use snakemake's `--notemp` and `--ignore-incomplete` options of snakemake (you can read about these in the snakemake help menu to understand what they do). Notice that `--additional-params` has to be the last thing that is passed to `anvi-run-workflow` in the command line, and only followed by  arguments of snakemake (i.e. arguments that are listed in the help menu of snakemake). The purpose here is to not limit any of the configuration that snakemake allows the user.

{:.warning}
Meren, should we add a note here about using screen? I thought of something like this:
Ok, let's run this. I always start by initiating a [screen](https://www.gnu.org/software/screen/manual/screen.html) session. If you are not familiar with what this is, basically we use it here, because we are running something that requires no user interaction for a long time on a remote machine (like a cluster node).

```
screen -S mysnakemakeworkflow
```

This is how we run the workflow on our cluster:

```
anvi-run-workflow -w metagenomics \
                            -c config-idba_ud.json \
                            --additional-params \
                                --cluster 'clusterize -log {log} -n {threads}' \
                                --jobs 20 \
                                --resource nodes=40
```

Let's talk a little more about the additional params. You can read about the `--cluster` parameter in the [snakemake docummentation](http://snakemake.readthedocs.io/en/stable/snakefiles/rules.html#threads). We use a wrapper for `qsub`, which we called `clusterize` in order to submit jobs to our cluster. The `{log}` and `{threads}` argument are part of snakemake's syntax, and you can learn about them from the snakemake docummentation. Briefly, `{threads}` is a wildcard that for each job submitted to the cluster will be replaced by the number of threads that you supplied in the config file for the specific rule (or the default number of threads that we set for that rule). `{log}` is a wildcard that will be replaced by the name of the log file that we set for each rule, unless you decided to change it, the log files would appear in your working directory under the directory `00_LOGS`.

Once you run this, you should see something like this:

[![merged_profile_idba_ud_snakemake_print]({{images}}/merged_profile_idba_ud_snakemake_print.png)]( {{images}}/merged_profile_idba_ud_snakemake_print.png){:.center-img .width-50}

The warning message in red letters is just there to make sure you are using the `--additional-params` argument properly (but you are reading this tutorial, so of course you are!). After that, there are a bunch of things in yellow and green letters that are printed by snakemake, that's good! snakemake is telling us what jobs it's going to run, and which resources were supplied.

Once everything finishes running (on our cluster it only takes 6 minutes as these are very small mock metagenomes), we can take a look at one of the merged profile databases:

```
anvi-interactive -p 06_MERGED/G02/PROFILE.db \
                 -c 03_CONTIGS/G02-contigs.db
```

And it should look like this:

[![merged_profile_idba_ud1]({{images}}/merged_profile_idba_ud1.png)]( {{images}}/merged_profile_idba_ud1.png){:.center-img .width-50}

Ok, so this looks like a standard merged profile database with two samples. As a bonus, we also added a step to import the number of short reads in each sample ("Total num reads"), and we also used it to calculate the percent of reads from the sample that have been mapped to the contigs ("Percent Mapped").

If you remember, we had two "groups" in the samples.txt file. Hence, we have two contigs databases and two merged profile databases. Let's take a look at the other profile database, but since this group only included one sample, then there was nothing to merge. What we do in this case, is that we automatically add the `--cluster-contigs` argument to `anvi-profile` (see the the help menu: `anvi-profile -h` for more details). We still create a "fake" merged profile database here: `06_MERGED/G01/PROFILE.db`, but this is just a mock output file, if you look into it you'll see:

```
$ cat 06_MERGED/G01/PROFILE.db
Only one file was profiled with G01 so there is nothing to
merge. But don't worry, you can still use anvi-interacite with
the single profile database that is here: 05_ANVIO_PROFILE/G01/sample_01/PROFILE.db
```


```bash
anvi-interactive -p 05_ANVIO_PROFILE/G01/sample_01/PROFILE.db \
                 -c 03_CONTIGS/G01-contigs.db
```

[![single_profile_idba_ud]({{images}}/single_profile_idba_ud.png)]( {{images}}/single_profile_idba_ud.png){:.center-img .width-50}

<div class="extra-info" markdown="1">

<span class="extra-info-header">A note on directory structure</span>

The default directory structure that will appear in the working directory includes these directories: "00\_LOGS", "01\_QC", "02\_FASTA", "03\_CONTIGS", "04\_MAPPING", "05\_ANVIO_PROFILE", "06\_MERGED"

Don't like these names? You can specify what is the name of the folder, by providing the following information in the config file:

```
    "output_dirs":{
        "LOGS_DIR" : "00_MY_beAuTiFul_LOGS",
        "QC_DIR" : "BEST_QC_DIR_EVER",
        "ASSEMBLY_DIR" : "assemblies",
        "CONTIGS_DIR" : "/absolute/path/to/my/contigs/dir",
        "MAPPING_DIR" : "relative/path/to/my/mapping/dir",
        "PROFILE_DIR": "/I/already/did/my/profiling/and/this/is/where/you/can/find/it/",
        "MERGE_DIR": "06_Keep_Calm_and_Merge_On"
    }
```

You can change all, or just some of the names of these output folders. And you can provide an absolute or a relative path for them.
</div>

In addition to the merged profile databases and the contigs databases (and all intermediate files), the workflow has another output, the QC report, which you can find here: `01_QC/qc-report.txt`. Let's look at it:

| sample    | number of pairs analyzed | total pairs passed | total pairs passed (percent of all pairs) | total pair_1 trimmed | total pair_1 trimmed (percent of all passed pairs) | total pair_2 trimmed | total pair_2 trimmed (percent of all passed pairs) | total pairs failed | total pairs failed (percent of all pairs) | pairs failed due to pair_1 | pairs failed due to pair_1 (percent of all failed pairs) | pairs failed due to pair_2 | pairs failed due to pair_2 (percent of all failed pairs) | pairs failed due to both | pairs failed due to both (percent of all failed pairs) | FAILED_REASON_P | FAILED_REASON_P (percent of all failed pairs) | FAILED_REASON_N | FAILED_REASON_N (percent of all failed pairs) | FAILED_REASON_C33 | FAILED_REASON_C33 (percent of all failed pairs) |
|-----------|--------------------------|--------------------|-------------------------------------------|----------------------|----------------------------------------------------|----------------------|----------------------------------------------------|--------------------|-------------------------------------------|----------------------------|----------------------------------------------------------|----------------------------|----------------------------------------------------------|--------------------------|--------------------------------------------------------|-----------------|-----------------------------------------------|-----------------|-----------------------------------------------|-------------------|-------------------------------------------------|
| sample_01 | 10450                    | 8423               | 80.6                                      | 0                    | 0                                                  | 0                    | 0                                                  | 2027               | 19.4                                      | 982                        | 48.45                                                    | 913                        | 45.04                                                    | 132                      | 6.51                                                   | 0               | 0                                             | 2027            | 100                                           | 0                 | 0                                               |
| sample_02 | 31350                    | 25550              | 81.5                                      | 0                    | 0                                                  | 0                    | 0                                                  | 5800               | 18.5                                      | 2777                       | 47.88                                                    | 2709                       | 46.71                                                    | 314                      | 5.41                                                   | 0               | 0                                             | 5800            | 100                                           | 0                 | 0                                               |
| sample_03 | 60420                    | 49190              | 81.41                                     | 0                    | 0                                                  | 0                    | 0                                                  | 11230              | 18.59                                     | 5300                       | 47.2                                                     | 5134                       | 45.72                                                    | 796                      | 7.09                                                   | 0               | 0                                             | 11230           | 100                                           | 0                 | 0                                               |

## The "all against all" option

The default behaviour for this workflow is to create a contigs database for each _group_ and map (and profile, and merge) the samples that belong to that _group_. If you wish to map all samples to all contigs, use the `all_against_all` option in the config file:

```
    "all_against_all": true
```

In your working directory you can find an updated config file `config-idba_ud-all-against-all.json`, that looks like this:

```json
{
    "samples_txt": "samples.txt",
    "idba_ud": {
        "--min_contig": 1000,
        "threads": 11,
        "run": true
    },
    "all_against_all": true
}
```

And we can generate a new workflow graph:

```bash
anvi-run-workflow -w metagenomics \
                  -c config-idba_ud-all-against-all.json \
                  --save-workflow-graph
```

An updated DAG for the workflow for our mock data is available below:

[![idba_ud-all-against-all]({{images}}/idba_ud-all-against-all.png)]( {{images}}/idba_ud-all-against-all.png){:.center-img .width-50}

A little more of a mess! But also has a beauty to it :-).

<div class="extra-info" markdown="1">

<span class="extra-info-header">A short advertisement for snakemake</span>
If you are new to **snakemake**, you might be surprised to see how easy it is to switch between modes. All we need to do is tell the **anvi_merge** rule that we want all samples merged for each _group_, and snakemake immediatly infers that it needs to also run the extra mapping, and profiling steps. *Thank you snakemake!* (says everyone).
</div>

## References Mode - Estimating occurence of population genomes in metagenomes

Along with assembly-based metagenomics, we often use anvi'o to explore the occurence of population genomes accross metagenomes. A good example of how useful this approach could be is described in this blogpost: [DWH O. desum v2: Most abundant Oceanospirillaceae population in the Deepwater Horizon Oil Plume](http://merenlab.org/2017/11/25/DWH-O-desum-v2/).
For this mode, what you have is a bunch of fastq files (metagenomes) and fasta files (reference genomes), and all you need to do is to let the workflow know where to find these files, using two `.txt` files: `samples_txt`, and `fasta_txt`. 

`fasta_txt` should be a 2 column tab-separated file, where the first column specifies a reference name and the second column specifies the filepath of the fasta file for that reference.

After properly formatting your `samples_txt` and `fasta_txt`, reference mode is initiated by adding these to your config file:

```
"fasta_txt": "fasta.txt",
"references_mode": true
```

The `samples_txt` stays as before, but this time the `group` column will specify for each sample, which reference should be used (aka the name of the reference as defined in the first column of `fasta_txt`). If the `samples_txt` file doesn't have a `group` column, then an ["all against all"](#the-all-against-all-option) mode would be provoked. 

First let's set up the reference fasta files:

```bash
gunzip three_samples_example/*.fa.gz
```

in your directory you can find the following `fasta.txt`, and `config-references-mode.json`:

```bash
$ cat fasta.txt
reference       path
G01     three_samples_example/G01-contigs.fa
G02     three_samples_example/G02-contigs.fa

$ cat config-references-mode.json
{
    "fasta_txt": "fasta.txt",
    "references_mode": true,
    "output_dirs": {
        "FASTA_DIR": "02_FASTA_references_mode",
        "CONTIGS_DIR": "03_CONTIGS_references_mode",
        "QC_DIR": "01_QC_references_mode",
        "MAPPING_DIR": "04_MAPPING_references_mode",
        "PROFILE_DIR": "05_ANVIO_PROFILE_references_mode",
        "MERGE_DIR": "06_MERGED_references_mode",
        "LOGS_DIR": "00_LOGS_references_mode"
    }
}
```

Let's create a workflow graph:

[![dag-references-mode]({{images}}/dag-references-mode.png)]( {{images}}/dag-references-mode.png){:.center-img .width-50}


<div class="extra-info" markdown="1">

<span class="extra-info-header">A note from Alon on why we need the references_mode flag</span>
This is a note that is mainly directed at anvi'o developers, so feel free to skip this note.

We could have just invoked "references_mode" if the user supplied a `fasta_txt`, but I decided to have a specific flag for it, to make things more verbose for the user.
</div>

Now we can run this workflow:

```bash
anvi-run-workflow -w metagenomics \
                  -c config-references-mode.json \
                  --additional-params \
                      --cluster 'clusterize -log {log} -n {threads}' \
                      --jobs 20 \
                      --resources nodes=40
```

# Contigs workflow

The contigs workflow is meant for cases in which all you want to is to only create contigs databases and annotate them with functions, taxonomy, etc. 

We can use the same fasta files that we used for the example above for references mode. Your working directory includes a config file `config-contigs.json`, let's first look at the workflow graph:

```bash
anvi-run-workflow -w contigs \
                  -c config-contigs.json \
                  --save-workflow-graph
```

[![DAG-contigs]({{images}}/DAG-contigs.png)]( {{images}}/DAG-contigs.png){:.center-img .width-50}

Now we could run this workflow:

```bash
anvi-run-workflow -w contigs \
                  -c config-contigs.json \
                  --additional-params \
                      --cluster 'clusterize -log {log} -n {threads}' \
                      --jobs 20 \
                      --resources nodes=40
```

If you want to get a default config file, then you can run:

```
anvi-run-workflow -w contigs \
                  --get-default-config CONFIG-NAME.json
```

# Pangenomics workflow

Running a [pangenomic workflow](http://merenlab.org/2016/11/08/pangenomics-v2/) with anvio is really easy. But now it is even easier. And the beauty of this workflow is that it would inherently include all the steps to annotate your contigs databases with what you wish (functions, hmms, taxonomy, etc.).

## Running the pangenomic workflow with external genomes

All you need are a bunch of fasta files, and a `fasta_txt`, formatted in the same manner that is described [above in references mode](#references-mode---estimating-occurence-of-population-genomes-in-metagenomes). In your working dir you can find the config `config-pangenomics.json`:

```
{
    "project_name": "MYPAN",
    "anvi_gen_genomes_storage": {
        "--external-genomes": "my-external-genomes.txt"
    },
    "fasta_txt": "fasta.txt",
    "output_dirs": {
        "FASTA_DIR": "01_FASTA_contigs_workflow",
        "CONTIGS_DIR": "02_CONTIGS_contigs_workflow",
        "LOGS_DIR": "00_LOGS_contigs_workflow"
    }
}
```

We can create a workflow graph:

```
anvi-run-workflow -w pangenomics \
                  -c config-pangenomics.json
                  --save-workflow-graph
```

[![DAG-pangenomics]({{images}}/DAG-pangenomics.png)]( {{images}}/DAG-pangenomics.png){:.center-img .width-50}

Since we used the same directories from the contigs workflow, then some of the steps don't have to be repeated. These steps are inside dashed line, whereas the rules that will be executed are inside a box.

# Running the workflow on a cluster

When submitting to a cluster, you can utilize the [snakemake cluster execution](http://snakemake.readthedocs.io/en/stable/executable.html#cluster-execution). Notice that the number of threads per rule could be changed using the `config.json` file (and not by using the [cluster configuration](http://snakemake.readthedocs.io/en/stable/executable.html#cluster-execution) file).

When submitting a workflow to a cluster, snakemake requires you to limit the number of jobs using `--jobs`. If you prefer to limit the number of threads that would be used by your workflow (for example, if you share your cluster with others and you don't want to consume all resources), then you can make use of the snakemake built-in `resources` directive. You can set the number of jobs to your limit (or to a very big number if you dont care), and use `--resources nodes=30`, if you wish to only use 30 threads. We used the word `nodes` so that to not confuse with the reserved word `threads` in snakemake.

Notice: if you don't include `--jobs` (identical to `--cores`) in your command line, then snakemake will only use one node, and will not utilize multiple nodes even if the `threads` parameter for a rule is higher than 1. This is simply the behaviour of snakemake (described [here](http://snakemake.readthedocs.io/en/stable/snakefiles/rules.html#threads)).

## A note on cluster-config

This note is here mainly for documentation of the code, and for those of you who are interested in snakemake. The reason we decided not to use the [cluster configuration](http://snakemake.readthedocs.io/en/stable/executable.html#cluster-execution) file to control the number of threads per rule, is becuase certain software require the number of threads as an input (for example `megahit` and `anvi-profile`), but the cluster config file is not available for shell commands within snakemake rules. To bypass this issue we simply put the threads configuration in the `config.json`, thus available for the user to modify.



# FAQs

## Is it possible to just do QC and then stop?

If you only want to qc your files and then compress them (and not do anything else), simply invoke the workflow with the following command:

```
anvi-run-workflow -w metagenomics -c config.json --additional-params --until gzip_fastqs
```

## Can I skip anvi-script-reformat-fasta?

In "reference mode", you may choose to skip this step, and keep your contigs names. In order to do so, add this to your config file:

```
	"anvi_script_reformat_fasta": {
		"run": false
	}
```

In assembly mode, this rule is always excecuted.

## What's going on behind the scenes before we run idba_ud?

A note regarding `idba_ud` is that it requires a single fasta as an input. Because of that, what we do is use `fq2fa` to merge the pair of reads of each sample to one fasta, and then we use `cat` to concatenate multiple samples for a co-assembly. The `fasta` file that is created is create as a temporary file, and is deleted once `idba_ud` finishes running. If this is annoying to you, then feel free to contact us or just hack it yourself.

## Can I change the parameters of samtools view?

The samtools command executed is:

```
samtools view additional_params -bS INPUT -o OUTPUT
```

Where `additional_params` refers to any parameters of samtools view that you choose to use (excluding `-bS` or `-o` which are always set by the workflow).For example, you could set it to be `-f 2`, or `-f 2 -q 1` (for a full list see the samtools [documentation](http://www.htslib.org/doc/samtools.html)). The default value for `additional_params` is `-F 4`.

## Can I change the parameters for bowtie2?

similar to [samtools](#can-i-change-the-parameters-of-samtools-view) we use the `additional_params` to configure bowtie2. The bowtie2 command executed is

```
bowtie2 --threads NUM_THREADS -x PREFIX_OF_BOWTIE_BUILD_OUTPUT -1 R1.FASTQ -2 R2.FASTQ additional_params -S OUTPUT.sam
```

Where `additional_params`. You can therefore use `additional_params` to specify all parameters that aren't `--threads`, `-x`, `-1`, `-2`, or `-S`. For example, if you don't want gapped alignment (aka the reference does not recruit any reads that contain indels with respect to it), and you don't want to store unmapped reads in the SAM output file, set `additional_params` to be `--rfg 10000,10000 --no-unal` (for a full list of options see the bowtie2 [documentation](http://bowtie-bio.sourceforge.net/bowtie2/manual.shtml#options)). The default is `--no-unal`.

# The architecture for anvi'o snakemake workflows

The purpose of this section is to give developers and power users a deeper understanding of how I designed this system. I hope this section and a clear architecture will be useful for us to 

* Add new workflows in the future with minimal effort,

* Understand and change existing ones with minimal effort,

* Develop user friendly warning and error messages so that the user could enjoy all the flexibility of the workflows.

When developing these workflows I made an effort to allow the user to easily configure any parameter that is accessible by the software that are utilized in the workflow. This was not always possible to do (under reasonable effort), as some constraints have to be made for the workflow to, well, 'work' and 'flow'.

## The anvi'o 'workflows' module

The `anvio.workflows` module contains libraries that help implement workflows. 

{:.notice}
We decided to maintain our snakemake [workflows](https://github.com/merenlab/anvio/tree/master/workflows/) in the anvi'o repository. This way, updating them whenever a change is required due to a change in the anvi'o programs will be straightforward, and we will be able to guarantee that the user would always have workflows that are compatible with the version of anvi'o they're using.

The main purpose of workflow files is to deal with configurable parameters of the rules in the workflow. By dealing, I mean enabling easy-access, and automated sanity checks. For convinience I always include `import anvio.workflows as w` at the head of each snakefile.

Each workflow corresponds to a class in `anvio.workflows.workflowsops`. Each of these classes inherits from `WorkflowSuperClass`, and in addition it inherits from any class corresponding to a workflow that is included in the workflow (i.e. if there is an `include` statement in the workflow). For example the `pangenome.snake` has the following line:

``` bash
include: w.get_path_to_workflows_dir() + "/generate_and_annotate_contigs_db.snake"`
```

{:.warning}
This sentence down below is not clear to me.

Which means it includes all the rules from `generate_and_annotate_contigs_db.snake` file, and accordingly the class `PangenomicsWorkflow` inherits from `ContigsDBWorkflow`.

An instance of the class `WorkflowSuperClass` has the following attributes:

|attribute|purpose|
|:--|:--|
|rules|a list of all the rules in the workflow|
|config|the config file dictionary|
|acceptable_params_dict|a dictionary with the rule names as keys. Each dictionaty entry is a list of the acceptable patameters for a rule, i.e. the parameters that could be changed via the config file.|
|dirs_dict|see below in "Definitions of directories in the workflow"|
|general_params|other workflow related parameters that could be found in the config file. These are all the parameters that are not related to just one specific rule (hence, "general"), and as such, they will not be nested under a rule name in the config file.|

### Sample config files
	
Each workflow takes a `config` file, which tells the workflow subsystem the specific user needs and requests to run the workflow expectedly. Each workflow class knows their entire list of configuration options, and they can provide an empty config files to be filled by the user. An example for the `PangenomicsWorkflow`:

``` python
>>> from anvio.workflows.workflowsops import PangenomicsWorkflow
>>> workflow = PangenomicsWorkflow(config)
>>> workflow.init()
>>> workflow.save_empty_config_in_json_format("empty-config-for-pangenomics.json")
```

Having an empty file with all possible paramters, the user can delete the ones they don't need, and run the workflow with the resulting config with something like,

``` bash
anvi-gen-empty-config --workflow WORKFLOW \
                      --config-file-name CONFIG
```

{:.notice}
If we are truly up to it, then we would make another module `get_default_config`, but for now it was just too much work.

### Definitions of directories in the workflow

In order to allow the user to define the names of directories through the config file, any directory that is expected to be created by the workflow must be defined in the `dirs_dict` in `anvio.workflows`, with a default name. For now this is what we have:

``` python
dirs_dict = {"LOGS_DIR"     : "00_LOGS",
             "QC_DIR"       : "01_QC",
             "ASSEMBLY_DIR" : "02_ASSEMBLY",
             "CONTIGS_DIR"  : "03_CONTIGS",
             "MAPPING_DIR"  : "04_MAPPING",
             "PROFILE_DIR"  : "05_ANVIO_PROFILE",
             "MERGE_DIR"    : "06_MERGED",
             "PAN_DIR"      : "07_PAN",
             "FASTA_DIR"    : "01_FASTA",
             "LOCI_DIR"     : "04_LOCI_FASTAS"   
}
```

The user-defined names from the config file, are set when the `WorkflowSuperClass.init()` is ran.

### The number of threads for a rule

In order to use the functionality of `threads:` in snakemake, each rule has the following lines included in the definition:

```
    threads: w.T(config, 'RULENAME', 1)
    resources: nodes = w.T(config, 'RULENAME', 1)
```

## Alon's rules for Defining rules

First of all, the default target rule (i.e. the first rule in the snakefile) is NOT called "all". Simply because if it were then when we use `includes:` (see it [here](http://snakemake.readthedocs.io/en/stable/snakefiles/modularization.html#includes) in the snakemake docummentation), then we would risk having multiple rules with the same name (which is not allowed).

This is the template I use for rules:

```
rule NEWRULE:
    version: 1.0
    log: dirs_dict["LOGS_DIR"] + "/{sample}-NEWRULE.log"
    input:
    output:
    params:
    threads: w.T(config, 'NEWRULE', 1)
    resources: nodes = w.T(config, 'NEWRULE', 1)
    shell: " >> {log} 2>&1"
```

I will use the rule for `anvi-pan-genome` from the pangenomic workflow as an example of how I write rules, and utilize the `anvio.workflows` package.

If a rule is basically wrapping a single program, then I set the name of the rule to be the same as the name of the program (except that any `-` has to be replaced by `_`). Below you can see the rule `anvi_pan_genome`:

```python
rule anvi_pan_genome:
    version: anvio.__pan__version__
    log: dirs_dict["LOGS_DIR"] + "/anvi_pan_genome.log"
    threads: w.T(config, "anvi_pan_genome", 20)
    resources: nodes = w.T(config, "anvi_pan_genome", 20)
    input: dirs_dict["PAN_DIR"] + "/" + project_name + "-GENOMES.db"
    params:
        output_dir = dirs_dict["PAN_DIR"],
        genome_names = w.B(config, "anvi_pan_genome", "--genome-names"),
        project_name = pan_project_name,
        skip_alignments = w.B(config, "anvi_pan_genome", "--skip-alignments"),
        align_with = w.B(config, "anvi_pan_genome", "--align-with"),
        exclude_partial_gene_calls = w.B(config, "anvi_pan_genome", "--exclude-partial-gene-calls"),
        use_ncbi_blast = w.B(config, "anvi_pan_genome", "--use-ncbi-blast"),
        minbit = w.B(config, "anvi_pan_genome", "--minbit"),
        mcl_inflation = w.B(config, "anvi_pan_genome", "--mcl-inflation"),
        min_occurrence = w.B(config, "anvi_pan_genome", "--min-occurrence"),
        min_percent_identity = w.B(config, "anvi_pan_genome", "--min-percent-identity"),
        sensitive = w.B(config, "anvi_pan_genome", "--sensitive"),
        description = w.B(config, "anvi_pan_genome", "--description"),
        overwrite_output_destinations = w.B(config, "anvi_pan_genome", "--overwrite-output-destinations"),
        skip_hierarchical_clustering = w.B(config, "anvi_pan_genome", "--skip-hierarchical-clustering"),
        enforce_hierarchical_clustering = w.B(config, "anvi_pan_genome", "--enforce-hierarchical-clustering"),
        distance = w.B(config, "anvi_pan_genome", "--distance"),
        linkage = w.B(config, "anvi_pan_genome", "--linkage")
    output: dirs_dict["PAN_DIR"] + "/" + pan_project_name + "-PAN.db"
    shell:
        """
            anvi-pan-genome -g {input} --num-threads {threads} -o {params.output_dir} {params.genome_names}\
            {params.skip_alignments} {params.align_with} {params.exclude_partial_gene_calls}\
            {params.use_ncbi_blast} {params.minbit} {params.mcl_inflation}\
            {params.min_occurrence} {params.min_percent_identity} {params.sensitive}\
            {params.project_name} {params.description} {params.overwrite_output_destinations}\
            {params.skip_hierarchical_clustering} {params.enforce_hierarchical_clustering}\
            {params.distance} {params.linkage}
        """
```

Ok so it looks like a lot, so let's dive in... In an effort to make things flexible I allow the user to change all parameters that are acceptable to `anvi-pan-genome`. In order to make things easy, I just named the parameters the same as the "double-dash" version of the arguments. This works for anvi'o most of the time as we make an effort to provide a comprehensive name to each argument.

To explain the module `w.B`, works slightly differently for arguments that are flags (such as `--use-ncbi-blast`) and arguments that accept an input (such as `--min-occurrence`). In both cases, if the argument is not defined in the config file, then it would return either an empty string or the default that was provided to `w.B`. For flags, it would simply return the argument (for example, `"--use-ncbi-blast"`), but for arguments such as `--min-occurrence` it would return `--min-occurrence VALUE` where `VALUE` is the value provided in the config file. That's why in the shell command it is enough to write `{params.min_occurrence}`, and there is no reason to write `--min-occurrence {params.min_occurrence}`.
