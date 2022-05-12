Overview
========

:::{admonition} Learning Objectives
- TODO
:::

This workshop is designed to introduce the steps involved in performing Optical
Character Recognition with Python. Included in this workshop is a description
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

OCR allows us to convert images of text documents into machine encoded text.
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

For this workshop we will be using Tesseract. Tesseract is free and open
source. It is developed by Google and has many independent contributors. The
most recent release, version 5.1.0 was released on March 1, 2022. Tesseract is
a command line tool as well as an OCR library. Tesseract can run on Windows,
MacOS, and Linux.  In addition to be free, there are many important advantages
to using Tesseract over other proprietary OCR tools. Tesseract is extremely
popular which makes it relatively easy to find resources online, such as guides
and stack overflow posts. In addition, there are many third party tools that
interface with Tesseract including GUIs.

While Tesseract is a command line tool, we will be running it through a Python
wrapper called 'pytesseract' for this workshop. This means that we will be
writing Python code to run Tesseract behind the scenes. A reason for running
Tesseract through python, instead of the command line, is it can make for more
reproducible workflows. This way all of the steps will be self documented
within your source code as well as its easier to connect the OCR step with the
other steps in the process - preprocessing, cleaning, which will also be done
in python.


## Prerequisites

Rather than installing python, tesseract, and the other software ourselves, we
will be using Conda - a software enivornment managmeent tool for data science.
Using Conda we will be able to install all of the packages necessary.

There are two option for installing Conda, 
Necessary software:
- [tesseract](https://github.com/tesseract-ocr/tesseract)
- [python](https://www.python.org/) Need version >=3.7

Useful software:
- [jupyter-lab](https://jupyterlab.readthedocs.io/en/stable/): Web based development environment
- [imagemagick](https://imagemagick.org/index.php): Free and open source tool for converting and editing images
- [pdftotext](https://www.xpdfreader.com/about.html): A tool for extracting text layers from pdfs
- [XnView](https://www.xnview.com/en/): an image and pdf viewer such as xnview

### Setup using Conda

#### install miniconda and create environment

#### install tesseract, pdftotext
#### install pytesseract, pypdf2, pandas, opencv, jupyter

