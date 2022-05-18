Introduction
========

:::{admonition} Learning Objectives
- TODO
:::

This workshop is designed to introduce the steps involved in performing **Optical
Character Recognition** with Python. Included in this workshop is a description
of common image preprocessing strategies for improving the quality of the OCR
output, a walk-through of tesseract usage through pytesseract, strategies for
quantifying OCR quality, ways to clean the text outputs, and a discussion on
ways of developing an effective and reproducible workflow for performing OCR on
a text corpus. The workshop will focus on images that contain English language
text, that has been printed, that is to say, not handwritten. Additionally, it
is focused on the tesseract open source library. This is an introductory
workshop which means its focus is on common ocr problems, and not on developing
strategies for training ocr, complex heuristics for document segmenting, nor is
it a deep dive on computer vision. This workshop also assumes that the data is
in a digitized form, albeit as images and not machine encoded text, and
therefore does not cover strategies for scanning/imaging physical documents.
Overall, learners should walk away with a sense of how to use tesseract ocr,
and a comprehension of some of the steps involved in a potential research
workflow that involves tesseract which includes image pre-processing and text
cleaning.

OCR allows us to convert images of text documents into **machine encoded text**.
There are many reasons to use OCR for research. Often times the most relevant
documents to your research query are not available in a digitized form. Or,
there does exist a scanned version of the document online, but that digital
document doesn't contain a machine-encoded text layer. There are even cases
where the document has a text layer, but OCR is still useful. This can occur if
the quality of the text layer is itself poor - perhaps it was generated with an
older version of the ocr software, perhaps it was manually catalogued but there
are many errors. Another use for OCR is for analysis that relies on spatial
data of the text on the page, that is lost in purely machine-encoded text
layer. For example, if you want to extract all the footnotes from an academic
paper, you may use OCR and leverage the positional data OCR returns.

For this workshop we will be using
[Tesseract](https://tesseract-ocr.github.io/).  Tesseract is free and open
source. It is developed by Google and has many independent contributors. The
most recent release, version 5.1.0 was released on March 1, 2022. Tesseract is
a command line tool as well as an OCR library. Tesseract can run on Windows,
MacOS, and Linux.  In addition to be free, there are many important advantages
to using Tesseract over other proprietary OCR tools. Tesseract is extremely
popular which makes it relatively easy to find resources online, such as guides
and stack overflow posts. In addition, there are many third party tools that
interface with Tesseract including GUIs.

While Tesseract is a command line tool, we will be running it through a
[Python](https://www.python.org/) wrapper called
[pytesseract](https://pypi.org/project/pytesseract/) for this workshop. This
means that we will be writing **Python** code to run Tesseract behind the
scenes. A reason for running Tesseract through python, instead of the command
line, is it can make for more reproducible workflows. This way all of the steps
will be self documented within your source code as well as its easier to
connect the OCR step with the other steps in the process - preprocessing,
cleaning, which will also be done in python.


## Prerequisites

For this course you will need [Python](https://www.python.org/),
[Tesseract](https://tesseract-ocr.github.io/), and several python packages.
While it is possible to install of these software individually, we *recommend*
using [Miniconda](https://docs.conda.io/en/latest/miniconda.html).

**Miniconda** is a minimal version of [Anaconda](https://www.anaconda.com/),
so, if you already have **Anaconda** installed on your system, you can go ahead
and continue using that.  Anaconda is a collection of free and open-source
data science software. Part of Anaconda is [conda](https://docs.conda.io)
which is a tool for installing and managing software on your system. We will be 
using conda to manage the installing of the software for this workshop. Both
Miniconda and Anaconda provide conda. Setup Miniconda by downloading and running 
the approrpriate installer for your operating system. Navigate to the [download page](https://docs.conda.io/en/latest/miniconda.html) and select the most recent version for your system. 

In addition, we will be using
[JupyterLab](https://jupyterlab.readthedocs.io/en/stable/). **JupyterLab** is
an IDE (integraded development environment) that runs in your browser. In this
environment we can install packages, run python code interactively, interface
with the command line tools we will be using, and manage our filesystems.

### Setup using Conda

After installing, verify that conda is set up by opening a terminal and running
conda. On windows this can be done by searching for 'Anaconda Prompt' in your
start menu.  for details. On MacOS, open the 'Terminal' application, which can
be found in `Applications -> Utilities -> Terminal.app`. On Linux, open your
favorite terminal emulator. With the terminal open, type the following command
and press enter: 

``` 
conda --version 
```

If this displays a line of information about the version, you are good to go.
For example on my machine I see `conda 4.12.0`.

With conda installed, we can install the other software for this workshop with
the following command. Copy and paste this command into your terminal and press
Enter:

```
conda install -c conda-forge tesseract poppler pdftotext pytesseract pypydf2 pandas jupyter jupyterlab
```

::: {note}
This command will install the list of named software on your system. The
section `-c conda-forge` specifies to `conda` to use the
[conda-forge](https://conda-forge.org/docs/user/introduction.html) channel. A
[channel](https://docs.conda.io/projects/conda/en/latest/user-guide/concepts/channels.html)
is a repository containing packages, with conda you can interface with many
different channels. The 'conda-forge' channel is a community contributed
channel with many additional packages that aren't provided by Anaconda's
default channel.
:::

::: {tip}
Conda can also be used to manage multiple environments. See [this
section](https://ucdavisdatalab.github.io/workshop_intermediate_python/chapters/02_reproducible.html#what-s-an-environment)
of DataLab's intermediate python reader for more information.  
:::
