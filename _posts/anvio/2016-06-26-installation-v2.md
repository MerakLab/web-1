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

This article explains basic steps of installing anvi'o using rather conventional methods.

Please consider opening an <a href="https://github.com/meren/anvio/issues">issue</a> for technical problems, or join us on Slack if you need help:

{% include _join-anvio-slack.html %}

<div class="extra-info" markdown="1">

<span class="extra-info-header">A note on Chrome</span>

Currently, the [Chrome Web Browser](https://www.google.com/chrome/browser/desktop/) has the most efficient SVG engine among all browsers we tested. For instance, Safari can run the anvi'o interactive interface, however it takes orders of magnitude more time and memory compared to Chrome. Firefox, on the other hand, doesn't even bother drawing anything at all. Long story short, the anvi'o interactive interface __will not perform optimally__ with anything but Chrome. So you need Chrome. Moreover, if Chrome is not your default browser, every time interactive interface pops up, you will need to copy-paste the address bar into a Chrome window. You can learn what is your default browser by running this command in your terminal:

``` bash
python -c 'import webbrowser as w; w.open_new("http://")'
```

</div>

## Painless installation with Conda

This is a very simple and effective way to install anvi'o on your system along with all of its dependencies (we thank [John Eppley](https://scholar.google.com/citations?user=4S2q_9cAAAAJ&hl=en) for pushing us towards this direction).

For this to work, you need [miniconda](https://docs.conda.io/en/latest/miniconda.html) to be installed on your system. If you are not sure whether it is installed or not, open a terminal (hopefully an [iTerm](https://www.iterm2.com/), if you are using Mac) and type `conda`. You should see an output like this instead of a 'command not found' error (your version might be different):

```bash
$ conda --version
conda 4.7.12
```

If you don't have conda installed, then you should first install it through their [installation page](https://docs.conda.io/en/latest/miniconda.html). Once you have confirmed you have conda installed, you are golden.

First run this command to make sure you are up-to-date:

```
conda update conda
```

Then, add the following channels that will be required for anvi'o installation:

```
conda config --env --add channels conda-forge
conda config --env --add channels bioconda
```

Then, create an anvi'o environment for anvi'o v{% include _project-anvio-version-number.html %}:

```
conda create -n anvio-6 python=3.6
```

It is critical to create it with Python 3.6.

Activate your new environment first:

```
conda activate anvio-6
```

Finally, install anvi'o:

```
conda install anvio=6
```

{:.warning}
If you are getting a `PackagesNotFoundError` error, it may mean that anvi'o v{% include _project-anvio-version-number.html %} is not yet synchronized to the conda repository. It usually takes a day or two to have new releases in conda. Please first check [the release logs for the latest version](https://github.com/merenlab/anvio/releases/tag/v{% include _project-anvio-version-number.html %}), and if it has been more than three days since the release, please let us know!


If the installation is complete, test anvi'o quickly to make sure everything is in order:

``` bash
anvi-self-test --suite mini
```

If your browser popped up at the end of this, you are golden.

{:.notice}
**If all tests seemed to run perfectly but the browser didn't pop-up**, it may mean that Python on your system is unable to find your default browser. Not a biggie. Paste the address http://localhost:8080 to your browser to see the interactive display. It may also mean that you are on a server system where there is no graphical interface for the browser to show up. That is fine, too. Read this article to learn how you can forward displays from servers to your laptop: [working with remote displays]({% post_url anvio/2018-03-07-working-with-remote-interative }).

***Note***: One of our users who has been trying conda installation on an HPC system [reported](https://github.com/merenlab/anvio/issues/895#issuecomment-403656800) the following steps working for them:

```
conda deactivate
conda create -y --name anvio-6 python=3.6
conda install -y --name anvio-6 -c bioconda -c conda-forge anvio=6
```

{:.warning}
**IMPORTANT NOTE**: You may need to activate the anvi'o conda environment every time you open a new terminal window. Depending on your conda setup, you will either need to run `source activate anvio-6` or `conda activate anvio-6` (this assumes you named your conda environment for anvio `anvio-6` as per the commands above using the `--name` flag --if not, please replace `anvio-6` with whatever you have used to name your environment). You can always list your conda environments by typing `conda env list`.

## Other installation (with varying levels of pain)

First things first: nothing here is as scary as it looks, and you can do it.

Firs, you will need to make sure your system does have all the following software if you are going to follow any of the following installation instructions. If you just follow these links, you will most probably be golden:

* [samtools]({% post_url anvio/2016-06-18-installing-third-party-software %}#samtools){:target="_blank"}
* [Prodigal]({% post_url anvio/2016-06-18-installing-third-party-software %}#prodigal){:target="_blank"}
* [HMMER]({% post_url anvio/2016-06-18-installing-third-party-software %}#hmmer){:target="_blank"}
* [SQLite]({% post_url anvio/2016-06-18-installing-third-party-software %}#sqlite){:target="_blank"}

Finally you will need `virtualenv`. This should work for most:

``` bash
pip install virtualenv
```

If you don't have `pip`, you will need to visit [this web page](https://pip.pypa.io/en/stable/installing/) to have it installed.

{:.notice}
**Please note**, anvi'o uses Python 3 exclusively.

{:.notice}
You may [run into some issues](https://matplotlib.org/faq/virtualenv_faq.html) **eith `matplotlib` in the virtual environment**. A simple [solution](https://matplotlib.org/faq/osx_framework.html#short-version) is to use [venv](https://docs.python.org/3/library/venv.html) (which comes built-in in python 3) instead of `virtualenv`. 

If you run into any trouble, send an e-mail to [Google Groups for anvi'o](https://groups.google.com/forum/#!forum/anvio).

OK. If made through the section above, you may have gone through the most painful part already, and anvi'o developers are very proud of you.


### Installing the latest stable release (safe mode)


{% include _join-anvio-slack.html %}

This is the best way to install the stable release (but not the best way if you would like to synchronize your anvi'o to the development version, for which you should jump to the 'active codebase' section).

You will do everything in a Python virtual environment. If you are not experienced with computer thingies, do not worry. If you have taken care of your dependencies mentioned above, the rest should be very simple.

We first need to create a new virtual environment for anvi'o. Since it is easier to keep all virtual environments in one place, I will first create a directory in my home:

``` bash
mkdir ~/virtual-envs/
```

Then we will create a new virtual environment for anvi'o under that directory, to activate it, and to check the Python version in it to make sure the version starts with `3`:

``` bash
virtualenv ~/virtual-envs/anvio-{% include _project-anvio-version-number.html %}
source ~/virtual-envs/anvio-{% include _project-anvio-version-number.html %}/bin/activate
python --version
```

{:.notice}
If using venv, run `python3 -m venv ~/virtual-envs/anvio-{% include _project-anvio-version-number.html %}`

{:.notice}
If using conda, run `python3 -m virtualenv ~/virtual-envs/anvio-{% include _project-anvio-version-number.html %}`

{:.notice}
The output of the last command must start with `Python 3`. If not, remove the virtual environment with `rm -rf ~/virtual-envs/anvio`, and find out how can you create a virtual environment for Python 3 on your system. You can try `-p python3` as a parameter to your `virtualenv` command. Or you can type `virtualenv` and _without pressing the space character_ press `TAB` key twice quickly to see if there is an alternative binary such as `virtualenv-3.6`. If not, it means Python 3 is not installed on your system.

Make sure your paths look alright. Yours should look similar to this:

``` bash
(anvio-{% include _project-anvio-version-number.html %}) meren ~ $ which pip
/Users/meren/virtual-envs/anvio-{% include _project-anvio-version-number.html %}/bin/pip
```

<div class="extra-info" markdown="1">

<span class="extra-info-header">A note on Python 3.7</span>

This box is only relevant to you if you are using Python version 3.7. In that case some dependencies will fail to install as their latest stable release is not compatible with this Python version. For these dependencies you need to install development versions.

**scikit-learn**

``` bash
pip install https://github.com/scikit-learn/scikit-learn/archive/4035e60a6f0a0a2546bf0442ab603961c6a9cc4a.zip
```

**Datrie (Dependency of snakemake)**

```
pip install https://github.com/ozcan/datrie/releases/download/0.7.1/datrie-0.7.1.tar.gz
```

Or from original source:

```
wget https://github.com/pytries/datrie/archive/0.7.1.tar.gz
tar xf 0.7.1.tar.gz
cd datrie-0.7.1
./update_c.sh
python3.7 setup.py build
python3.7 setup.py install
```

After installing these dependencies you should be able to install anvi'o. But when you run `anvi-profile --version`. The anvi'o version may show up as `vunknown`. This happens when the version of an anvi'o depenceny does not match to what anvi'o expects. You can always check the actual version of anvi'o with `pip show anvio` or  `pip list | grep anvio`.

</div>

Now you can do the installation:

``` bash
pip install numpy
pip install scipy
pip install cython
pip install anvio
```

If all looks good, now you should be able to run `anvi-self-test`:

``` bash
anvi-self-test --suite mini
```

{:.notice}
You may see warning messages during self-test runs. Don't be concerned.

If this runs successfully, a browser window will popup. Don't forget to go back to your terminal and press `CTRL+C` to kill the server. To leave the virtual environment, you can run the command `deactivate`.

Now every time you want to use anvi'o, you will need to activate the virtual environment. If you like things to be convenient as much as we do, you may want to run the following command so you have a new command, `anvi-activate` that activates your anvi'o installation:

``` bash
echo 'alias anvi-activate-v{% include _project-anvio-version-number.html %}="source ~/virtual-envs/anvio-{% include _project-anvio-version-number.html %}/bin/activate"' >> ~/.bash_profile
```

When I open a new terminal, things look like this:

``` bash
meren ~ $ anvi-interactive -v
-bash: anvi-interactive: command not found
meren ~ $ anvi-activate-v{% include _project-anvio-version-number.html %}
(anvio) meren ~ $ anvi-interactive -v
Anvi'o version ...............................: {% include _project-anvio-version-number.html %}
Profile DB version ...........................: 20
Contigs DB version ...........................: 8
Pan DB version ...............................: 5
Samples information DB version ...............: 2
Genome data storage version ..................: 1
Auxiliary data storage version ...............: 3
Anvi'server users data storage version .......: 1
(anvio) meren ~ $ 
```

### Installing or updating from the active codebase (because why not)

This will allow you to go beyond the stable version and follow the very current version of the codebase (we assume you already have taken of your dependencies).

Let's setup a new virtual environment and activate it:

``` bash
virtualenv ~/virtual-envs/anvio-dev
source ~/virtual-envs/anvio-dev/bin/activate
python --version
```

{:.notice}
If using conda, run `python3 -m virtualenv ~/virtual-envs/anvio-dev`

Don't forget to make sure the output of the last command starts with `Python 3`.

### I need to get the codebase

So this is your first time with the codebase. Get a fresh copy (with all the submodules necessary):

``` bash
cd
git clone --recursive https://github.com/meren/anvio.git
```

Then go into the `anvio` directory, and then run the installation:

``` bash
cd anvio
source ~/virtual-envs/anvio-dev/bin/activate
pip install -r requirements.txt
python setup.py install
```

### I already have the codebase

So you want to _update_ your already existing installation. Follow these steps:

{% highlight bash %}
cd
cd anvio
git pull
git submodule update --init --recursive
source ~/virtual-envs/anvio-dev/bin/activate
pip install -r requirements.txt
python setup.py install
{% endhighlight %}

### What now?

Now it is time to run `anvi-self-test --suite mini`, of course.

If you want to make things simpler, you can add an alias to your `~/.bash_profile` to easily switch to this environment:

``` bash
echo 'alias anvi-activate-dev="source ~/virtual-envs/anvio-dev/bin/activate"' >> ~/.bash_profile
```

### Installation for developers (you're a wizard, arry)

{:.notice}
This is the best option to keep up-to-date with day-to-day updates from anvi'o developers.

If you are planning to do this, you really need no introductions, but I will give you one anyway. Clone the codebase into a `$DIR` you like:

{% highlight bash %}
cd $DIR
git clone --recursive https://github.com/meren/anvio.git
{% endhighlight %}

Create a virtual environment (`master` to remind you that you are following the GitHub `master`), and do the initial setup, and leave it:

{:.notice}
If using conda, run `python3 -m virtualenv ~/virtual-envs/anvio-master` instead of the line that starts wth `virtualenv` down below.

``` bash
virtualenv ~/virtual-envs/anvio-master
source ~/virtual-envs/anvio-master/bin/activate
python --version # make sure the output starts with `Python 3`.
cd $DIR/anvio # don't forget to update the $DIR with the real path
pip install -r requirements.txt
python setup.py build
cp build/lib.*/anvio/*so anvio/
rm -rf anvio.egg-info build dist
deactivate
```

Then update your activation batch to add necessary environment variables:

{:.warning}
Please note that **you need to update** the `$DIR` variable to whichever directory you cloned the codebase on your system **before running the following lines in your terminal**.


``` bash
echo 'export PYTHONPATH=$PYTHONPATH:$DIR/anvio/' >> ~/virtual-envs/anvio-master/bin/activate
echo 'export PATH=$PATH:$DIR/anvio/bin:$DIR/anvio/sandbox' >> ~/virtual-envs/anvio-master/bin/activate
```

That's it. If you like, add an alias to your `~/.bash_profile` to activate this quickly:

``` bash
echo 'alias anvi-activate-master="source ~/virtual-envs/anvio-master/bin/activate"' >> ~/.bash_profile
source ~/.bash_profile
```

Finally, if you would like to pull the latest commits from GitHub every time you switch to the `master`, add these to your activation batch (you will need to update `$DIR` once again):

``` bash
echo 'cd $DIR/anvio && git pull && cd -' >> ~/virtual-envs/anvio-master/bin/activate
```

You are golden.


## Running the "Mini Test"

You can make anvi'o test itself by running the program `anvi-self-test`. It is absolutely normal to see 'warning' messages. In most cases anvi'o is talkative, and would like to keep you informed. **You should read those warning messages carefully, but they often don't require any action.**

Upon the successful completion of all the tests, your browser should popup and take you to the interactive interface. When you click that 'Draw' button whenever you see one. One of those interfaces should look something like this (this is one of the older version of [the anvi'o interactive interface]({% post_url anvio/2016-02-27-the-anvio-interactive-interface %}), and it shall stay here so we remember where we came from):

<div class="centerimg">
<a href="{{ site.url }}/images/anvio/misc/mini-test-screenshot.png"><img src="{{ site.url }}/images/anvio/misc/mini-test-screenshot.png" width="50%" /></a>
</div>

All fine? Perfect! Now you have a running installation of anvi'o!

It is time to go through some anvi'o tutorials (see the pull-down menu at the top of this page), or take a look at [all the other posts on the platform]({{ site.url }}/software/anvio).
