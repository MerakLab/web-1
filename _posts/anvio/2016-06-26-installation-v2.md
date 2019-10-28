---
layout: post
title: "Installing anvi'o"
excerpt: "Instructions to install the v2 branch of the platform."
modified: 2019-05-14
tags: []
categories: [anvio]
redirect_from:
 - /2015/05/01/installation/
 - /install-anvio
comments: true
image:
  feature: https://github.com/merenlab/anvio/raw/master/anvio/data/interactive/images/logo.png
---

{% include _toc.html %}

{% include _project-anvio-version.html %}

This article explains basic steps of installing anvi'o using rather conventional methods both for end users and current of future developers.

We do recommend you to install anvi'o on your system, but if you just want to run it without any installation, you may find the article on [trying anvi'o in docker]({% post_url anvio/2015-08-22-docker-image-for-anvio %}) more suitable.

Please consider opening an <a href="https://github.com/meren/anvio/issues">issue</a> for technical problems, or join us on Slack if you need help:

{% include _join-anvio-slack.html %}

## Installation with conda (painless method suggested for everyone)

This is a very simple and effective way to install anvi'o on your system along with most of its dependencies.

For this to work, you need [miniconda](https://docs.conda.io/en/latest/miniconda.html) to be installed on your system. If you are not sure whether it is installed or not, open a terminal (hopefully an [iTerm](https://www.iterm2.com/), if you are using Mac) and type `conda`. You should see an output like this instead of a 'command not found' error (your version might be different):

```bash
$ conda --version
conda 4.7.12
```

If you don't have conda installed, then you should first install it through their [installation page](https://docs.conda.io/en/latest/miniconda.html). Once you have confirmed you have conda installed, run this command to make sure you are up-to-date:

``` bash
conda update conda
```

{:.warning}
Please make sure you create a new conda enviornment for anvi'o (you can make sure you are not in a conda environment by opening a new terminal and running `conda deactivate`. You can see which environments exist on your coputer by running `conda env list`. You can indeed install anvi'o in an existing conda environment, but if things go wrong, please consider relying on meditation for help rather than [anvi'o community resources]({% post_url anvio/2019-10-07-getting-help %}).

Then, add the following channels that will be required for the anvi'o installation:

``` bash
conda config --env --add channels conda-forge
conda config --env --add channels bioconda
```

Then, create an anvi'o environment for anvi'o v{% include _project-anvio-version-number.html %} (it is essential to create it with Python 3.6 as shown below):

``` bash
conda create -n anvio-6.1 python=3.6
```

Once it is ready, activate your new environment:

``` bash
conda activate anvio-6.1
```

And finally install anvi'o in it:

``` bash
conda install -y anvio=6.1
```

This may take a while.

{:.notice}
**You are getting a `PackagesNotFoundError` error?**. It may mean that anvi'o v{% include _project-anvio-version-number.html %} is not yet synchronized to the conda repository. It usually takes a day or two to have new releases in conda. Please first check [the release logs for the latest version](https://github.com/merenlab/anvio/releases/tag/v{% include _project-anvio-version-number.html %}), and if it has been more than three days since the release, please let us know!

When the last step is complete, there is one last thing we need to do. Unfortunately conda installs the wrong version of diamond, so here we will make sure we go back to the correct version:

```bash
conda install -y diamond=0.9.14
```

Once this is complete, you should test anvi'o quickly to make sure everything is in order:

``` bash
anvi-self-test --suite mini
```

If at the end of this your browser automatically loaded the test run results, you are golden.

{:.notice}
**Your browser didn't pop-up?**. If all tests seemed to run perfectly but the browser didn't pop-up, it may mean that Python on your system is unable to find your default browser. Not a biggie (but see the note on Chrome down bellow and try the Python command shown there). Paste the address http://localhost:8080 to your browser to see the interactive display. It may also mean that you are on a server system where there is no graphical interface for the browser to show up. That is fine, too. Read this article to learn how you can forward displays from servers to your laptop: [working with remote displays]({% post_url anvio/2018-03-07-working-with-remote-interative %}).

***Note***: One of our users who has been trying conda installation on an HPC system [reported](https://github.com/merenlab/anvio/issues/895#issuecomment-403656800) the following steps working for them:

``` bash
conda deactivate
conda create -y --name anvio-6.1 python=3.6
conda install -y --name anvio-6.1 -c bioconda -c conda-forge anvio=6.1
conda install -y diamond=0.9.14
```


{:.warning}
**IMPORTANT NOTE**: You may need to activate the anvi'o conda environment every time you open a new terminal window. Depending on your conda setup, you will either need to run `source activate anvio-6.1` or `conda activate anvio-6.1` (this assumes you named your conda environment for anvio `anvio-6.1` as per the commands above using the `--name` flag --if not, please replace `anvio-6.1` with whatever you have used to name your environment). You can always list your conda environments by typing `conda env list`.

<div class="extra-info" markdown="1">

<span class="extra-info-header">A note on Chrome</span>

Currently, the [Chrome Web Browser](https://www.google.com/chrome/browser/desktop/) has the most efficient SVG engine among all browsers we tested. For instance, Safari can run the anvi'o interactive interface, however it takes orders of magnitude more time and memory compared to Chrome. Firefox, on the other hand, doesn't even bother drawing anything at all. Long story short, the anvi'o interactive interface __will not perform optimally__ with anything but Chrome. So you need Chrome. Moreover, if Chrome is not your default browser, every time interactive interface pops up, you will need to copy-paste the address bar into a Chrome window. You can learn what is your default browser by running this command in your terminal:

``` bash
python -c 'import webbrowser as w; w.open_new("http://")'
```

</div>

If you are here, you are done. Congratulations, and thank you!

## Following the active codebase (you're a wizard, arry)

If you follow these instructions you can follow the `master` branch of anvi'o, which is where we add new features and bug fixes in between stable releases. Following the `master` branch as prescribed here will not prevent you to also have a stable anvi'o ersion on the same computer in parallel.

{:.warning}
This section is not meant to be followed by those who would define themselves as *end users* in a conventional sense. It means if you would consider yourself someone who feels more comfortable with *stability*, *calmness*, and *predictibility* rather than *advanture* and *suprise*, or if you think feeling the occasional urge to ask your computer *WHAT NOW YOU STUPID CALCULATOR?* is not your cup of tea, you should stick with the installation recipe described in the previous section. Because if you just found yourself in this section and do not know what is `git` or `master`, you may be about to take upon more work than you anticipate (and while Meren totally thinks you should do it anyway because you have the world in your grip and all these unknown is yours to conquer, those who know how to cultivate happiness and productivity at the some time will suggest you to don't do it and move on with your day).

Following the active codebase will come with various advantages, but you must also consider the fact that it can be less stable than official releases. Nevertheless, here we go for those of you who live life at the edge.

{:.notice}
Special thanks go to [Jarrod Scott](https://twitter.com/metacrobe) who tested this recipe out and suggested changes. If you something doesn't work here anymore please fix it, and share your solution with us.

First, create a new conda environment, and activate it:

``` bash
conda deactivate
conda create -y --name anvio-master python=3.6
conda activate anvio-master
```

If this all worked, these are the outputs you should see:

``` bash
(anvio-master) meren ~ $ python --version
Python 3.6.7

(anvio-master) meren ~ $ which python
/Users/meren/miniconda3/envs/anvio-master/bin/python
```

Good? Good. Then, install the following dependencies in this conda environment:

``` bash
pip install virtualenv

conda install -y -c bioconda prodigal
conda install -y -c bioconda mcl
conda install -y -c bioconda muscle
conda install -y -c bioconda fasttree
conda install -y -c bioconda hmmer
conda install -y -c bioconda blast
conda install -y -c bioconda megahit
conda install -y -c bioconda bowtie2
conda install -y -c bioconda bwa
conda install -y -c bioconda samtools
conda install -y -c bioconda centrifuge
conda install -y -c bioconda diamond=0.9.14
conda install -y -c bioconda bioconductor-qvalue
conda install -y r-base=3.6.1
conda install -y -c r r-tidyverse
conda install -y -c conda-forge r-optparse
```

Now it is time to get a copy of the anvi'o codebase. Here I will suggest `~/github/` as the base directory, but you can change if you want to something else (in which case you must remember to apply that change all the following commands, of course):

``` bash
# setup the code directory and get the anvi'o codebase
mkdir -p ~/github && cd ~/github/
git clone --recursive https://github.com/meren/anvio.git
```

Here we will setup a directory to keep the Python virtual environment for anvi'o (virtual environment within a virtual environment, keep your totem nearby):

``` bash
mkdir -p ~/virtual-envs/

# run this just in case there is already a directory with this name
# so you avoid 'too many symlinks' error from virtualenv in the
# next step
rm -rf ~/virtual-envs/anvio-master

virtualenv ~/virtual-envs/anvio-master
source ~/virtual-envs/anvio-master/bin/activate
```

Now it is time to install the Python dependencies of anvi'o:

``` bash
cd ~/github/anvio/
pip install -r requirements.txt
```

When this is done successfully, you can deactivate the Python environment:

```
deactivate
```

Now we will setup your conda environment in such a way, every time you activate anvi'o within it, you will get the very latest updates from the `master` repository:

``` bash
# updating the activation script for the Python virtual environmnet
# so (1) Python knows where to find anvi'o libraries, (2) BASH knows
# where to find its programs, and (3) every the environment is activated
# it downloads the latest code from the `master` repository
echo -e "\n# >>> ANVI'O STUFF >>>" >> ~/virtual-envs/anvio-master/bin/activate
echo 'export PYTHONPATH=$PYTHONPATH:~/github/anvio/' >> ~/virtual-envs/anvio-master/bin/activate
echo 'export PATH=$PATH:~/github/anvio/bin:~/github/anvio/sandbox' >> ~/virtual-envs/anvio-master/bin/activate
echo 'cd ~/github/anvio && git pull && cd -' >> ~/virtual-envs/anvio-master/bin/activate
echo "# <<< ANVI'O STUFF <<<" >> ~/virtual-envs/anvio-master/bin/activate
```

Finally we define an alias, `anvi-activate-master`, so when you are in your conda environment for `anvio-dev` you can run it as a command to initiate everything like a pro:

```
echo -e "\n# >>> ANVI'O STUFF >>>" >> ~/.bash_profile
echo 'alias anvi-activate-master="source ~/virtual-envs/anvio-master/bin/activate"' >> ~/.bash_profile
echo "# <<< ANVI'O STUFF <<<" >> ~/.bash_profile
source ~/.bash_profile
```

At this stage if you run `anvi-activate-master`, you should see similar outputs to these:

```
$ anvi-self-test -v

Anvi'o version ...............................: esther (v6.1-master)
Profile DB version ...........................: 31
Contigs DB version ...........................: 14
Pan DB version ...............................: 13
Genome data storage version ..................: 6
Auxiliary data storage version ...............: 2
Structure DB version .........................: 1

$ which anvi-self-test

/Users/meren/github/anvio/bin/anvi-self-test
```

If that is the case, you're golden.


<details markdown="1"><summary>Show/hide playing with the code</summary>

Now you can go to the anvi'o codebase,

```
cd ~/github/anvio
```

Make change,

``` bash
# as you probably know the following line is good for Mac
# computers but will fail in Linux systems unless you remove
# the part that goes like -i '' :)
sed -i '' 's/esther/ESTHER/g' anvio/__init__.py
```

See it in the git log:

``` diff
$ git diff

diff --git a/anvio/__init__.py b/anvio/__init__.py
index 1ceca28a..75c91a73 100644
--- a/anvio/__init__.py
+++ b/anvio/__init__.py
@@ -13,7 +13,7 @@ import platform
 import pkg_resources

 anvio_version = '6.1-master'
-anvio_codename = 'esther'
+anvio_codename = 'ESTHER'

 DEBUG = '--debug' in sys.argv
 FORCE = '--force' in sys.argv
```

But also see in action:

```
$ anvi-self-test -v

Anvi'o version ...............................: ESTHER (v6.1-master)
Profile DB version ...........................: 31
Contigs DB version ...........................: 14
Pan DB version ...............................: 13
Genome data storage version ..................: 6
Auxiliary data storage version ...............: 2
Structure DB version .........................: 1
```

If you want to see what is going on in the codebase lately install `tig`,

``` bash
pip install tig
```

And run it:

``` bash
cd ~/github/anvio/
tig
```
</details>

So, this is the end of setting up the active anvi'o codebase on your computer so you can follow our daily additions to the code before they appear in stable releases, and use anvi'o exactly the way we use on our comptuers for our science on your own risk. Please note that given this setup so far, every time you open a terminal you will have to first activate conda, and then the Python environment:

```
conda activate anvio-master
anvi-activate-master
```

You can always use `~/.bashrc` or `~/.bash_profile` files to add aliases to make these steps easier for yourself, or remove them when you are tired.

<details markdown="1"><summary>Show/hide Meren's BASH profile setup</summary>

This is all personal taste and they may need to change from computer to computer, but I adeed the following lines at the end of my `~/.bash_profile` to easily switch between different versions of anvi'o on my Mac system:

{:.notice}
If you are using Anaconda rather than miniconda, or you are using Linux and not Mac, you will have to find corresponding paths for lines that start with `/Users` down below :)

``` bash
init_anvio_stable () {
    {
        deactivate && conda deactivate
    } &> /dev/null

    export PATH="/Users/$USER/miniconda3/bin:$PATH"
    . /Users/$USER/miniconda3/etc/profile.d/conda.sh
    conda activate anvio-6.1
    export PS1="\[\e[0m\e[47m\e[1;30m\] :: anvi'o v6.1 :: \[\e[0m\e[0m \[\e[1;32m\]\]\w\[\e[m\] \[\e[1;31m\]>>>\[\e[m\] \[\e[0m\]"
}


init_anvio_master () {
    {
        deactivate && conda deactivate
    } &> /dev/null

    export PATH="/Users/$USER/miniconda3/bin:$PATH"
    . /Users/$USER/miniconda3/etc/profile.d/conda.sh
    conda activate anvio-master
    anvi-activate-master
    export PS1="\[\e[0m\e[40m\e[1;30m\] :: anvi'o v6 master :: \[\e[0m\e[0m \[\e[1;34m\]\]\w\[\e[m\] \[\e[1;31m\]>>>\[\e[m\] \[\e[0m\]"
}

alias as=init_anvio_stable
alias am=init_anvio_master
```

With this steup, in a new terminal window I can type `as` or `am` to run the stable or master version of anvi'o, or to switch from one to the other:

```
meren ~ $ anvi-self-test -v
-bash: anvi-self-test: command not found

meren ~ $ as

:: anvi'o v6.1 :: ~ >>>

:: anvi'o v6.1 :: ~ >>> anvi-self-test -v
Anvi'o version ...............................: esther (v6.1)
Profile DB version ...........................: 31
Contigs DB version ...........................: 14
Pan DB version ...............................: 13
Genome data storage version ..................: 6
Auxiliary data storage version ...............: 2
Structure DB version .........................: 1

:: anvi'o v6.1 :: ~ >>> am

:: anvi'o v6 master :: ~ >>>

:: anvi'o v6 master :: ~ >>> anvi-self-test -v
Anvi'o version ...............................: esther (v6-master)
Profile DB version ...........................: 31
Contigs DB version ...........................: 14
Pan DB version ...............................: 13
Genome data storage version ..................: 6
Auxiliary data storage version ...............: 2
Structure DB version .........................: 1
```


**But please note** that both aliases run `deactivate` and `conda deactivate` first, and they may not work for you especially if you have a fancy setup. I'd be very happy to improve these shortcuts.
</details>


## Other installation (with varying levels of pain)

If you are an end user we really suggest you to follow the installation instructions for conda. But then it is your computer, nothing here is as scary as it looks, and you can do it.

You will need to make sure your system does have all the following software if you are going to follow any of the following installation instructions. If you just follow these links, you will most probably be golden:

* [samtools]({% post_url anvio/2016-06-18-installing-third-party-software %}#samtools){:target="_blank"}
* [Prodigal]({% post_url anvio/2016-06-18-installing-third-party-software %}#prodigal){:target="_blank"}
* [HMMER]({% post_url anvio/2016-06-18-installing-third-party-software %}#hmmer){:target="_blank"}
* [SQLite]({% post_url anvio/2016-06-18-installing-third-party-software %}#sqlite){:target="_blank"}

Then we suggest you to use `virtualenv` to start a Python 3.6 environment, and install anvi'o in it. Don't use `pip` as the anvi'o package stored at PyPI is lacking some files due to size limitatons. Instead, visit the following link, go to the bottom of the page, downlaod the file `anvio-X.tar.gz` and work with that file:

[https://github.com/merenlab/anvio/releases/latest](https://github.com/merenlab/anvio/releases/latest)


## Running the "Mini Test"

You can make anvi'o test itself very quickly by running,

``` bash
anvi-self-test --suite mini
```

It is absolutely normal to see 'warning' messages. In most cases anvi'o is talkative, and would like to keep you informed. **You should read those warning messages carefully, but in most cases they will not require action.**

Upon the successful completion of all the tests, your browser should popup and take you to the interactive interface. When you click that 'Draw' button whenever you see one.

When anvi'o is done drawing the test data, you should see something like this:

{:.notice}
This is one of the older version of [the anvi'o interactive interface]({% post_url anvio/2016-02-27-the-anvio-interactive-interface %}), and it shall stay here so we remember where we came from.

<div class="centerimg">
<a href="{{ site.url }}/images/anvio/misc/mini-test-screenshot.png"><img src="{{ site.url }}/images/anvio/misc/mini-test-screenshot.png" width="50%" /></a>
</div>

All fine? Perfect! Now you have a running anvi'o!

It is time to go through some anvi'o tutorials (see the pull-down menu at the top of this page), or take a look at [all the other posts on the platform]({{ site.url }}/software/anvio).
