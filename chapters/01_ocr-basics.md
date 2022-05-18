---
jupytext:
    formats: md:myst
    text_representation:
        extension: .md
        format_name: myst
kernelspec:
    display_name: Python 3
    language: python
    name: python3
---

```{code-cell}
:tags: [remove-cell]
import os

os.chdir("..")
```

OCR With Pytesseract
====================

## Setup

For this workshop, we will be using a sample set of images prepared to demonstrate
some key ocr concepts. Download the [zip file]() and extract it to a directory
where you are keeping your notes. 

Start by importing `pandas` and the `pytesseract` package into your python session with:
```{code-cell}
import pandas as pd
import pytesseract
```

We can verify that tesseract is also installed, as well as which version is being
used with the `get_tesseract_version` function:
```
pytesseract.get_tesseract_version()
```

Next, lets create names for some of the prepared images that we will refer 
to throughout this workshop.
```{code-cell}
simple_img = './data/alice_start-gutenberg.jpg'
fr_img = './data/fr_ocr-wikipedia.png'
kor_img = './data/kor_ocr-wikipedia.png'
toc_img = './data/alice_toc-gutenberg.jpg'
two_column_img = './data/two_column-google.png'
```

::: {note} 
**Supported Image Formats:**

pytesseract can operate on any PIL Image, NumPy array or file path of an image
than can be processed by Tessseract. Tesseract supports most image formats:
png, jpeg, tiff, bmp, gif.

Notably, pytesseract, and tesseract, don't work on Pdf files. In order to
perform OCR on a pdf file, you must first convert it to a supported image
format. 
:::

## Usage

In order to maximize the quality of results from OCR with tesseract, its often
necessary to customize the behavior of the OCR through parameters. With
tesseract, you can specify one or multiple languages you expect in the
document, which OCR engine to use, and information about the layout of the text
within the document.  

Tesseract by default uses its english training data. Tesseract detects
characters and then tries to map the detected characters to its closest
neighbor. Both of these processes are greatly effected by the assumed language
of the text. With tesseract you can specify the language or languages for the
OCR engine to use. Tesseract can be configured to use different OCR 'engine
modes'. This can be very useful when working with software or on systems that
don't support the newest engines or for which computational performance is a
limiting factor. In addition, not all languages have training data for each
engine mode. Tesseract also supports different behaviors for how it expects the
text to be laid out on the page. For example it supports options for if the
image is expected to contain just a single character, a single line, multiple
columns, and several others.

In addition to modifying the behavior of the OCR engine, we can configure the
format of the output. Including how much information we want about the
extracted text - such as the location on the page and confidence values for the
extracted text.

### Simplest Usage

The simplest way to get the text from an image with pytesseract is with `pytesseract.image_to_string`:
```{code-cell}
pytesseract.image_to_string(simple_img)
```	

This returns just a string of all the text detected in the image. Notice that
the returned text contains alphabetical characters, digits, and escape
characters such as `\n` which represents a newline. The entire text has been
concatenated into a single python string, aggregating all the lines, and words
detected on the page by tesseract.

This is the simplest way to extract the text from an image, when invoked
without additional parameters, the `image_to_string` function uses the default
usage options of tesseract. 

### Language Specification

By default, tesseract uses its english training data. This can lead to very
poor results if there are non english characters in the image. This is
especially true if the image contains text that doesn't use a latin alphabet.
Lets look at an example using an image containing French text, and another image 
containing Korean text. As before, we will be invoking the function without 
any additional parameters:

```{code-cell}
pytesseract.image_to_string(fr_img)
```

```{code-cell}
pytesseract.image_to_string(kor_img)
```

Notice that these results are not ideal. While the French string is quite
close, there are a couple of errors with the accents on the characters, and the
Korean string is pretty much useless.

In order to use ocr on languages other than english we need to download the
language's associated training data for tesseract. The `tesseract` package we
installed with conda-forge comes with most of the language training data. Data
for tesseract can be found at the [tessdata github
repository](https://github.com/tesseract-ocr/tessdata). 

With pytesseract we can see all the available languages with:
```{code-cell}
:tags: ["output_scroll"]
pytesseract.get_languages()
```

To specify the language to use, pass the name of the language as a parameter to
`pytesseract.image_to_string`. Lets rerun the ocr on the korean image, this
time specifying the appropriate language.

```{code-cell}
pytesseract.image_to_string(kor_img, lang='kor')
```

Tesseract supports images that contain multiple languages, we can specify which
languages to use by separating them with the `+` character in the configuration
string:

```{code-cell}
pytesseract.image_to_string(kor_img, lang='kor+eng')
```

### Engine Selection

Tesseract has several **engine modes** that can be used. There are two main
implementations - the original tesseract engine, and, since Tesseract version
4, an LSTM based OCR engine. In addition, Tesseract supports using a
combination of the two. The list of Tesseract's engine modes:

```
0 = Original Tesseract only.
1 = Neural nets LSTM only.
2 = Tesseract + LSTM.
3 = Default, based on what is available.
```

By default Tesseract uses mode 3, which is generally equivalent to option 2.

To set the 'oem' (OCR engine mode) with pytesseract we pass it as the 'config' parameter:
```{code-cell}
custom_oem_psm_config = r'--oem 1'
pytesseract.image_to_string(simple_img, config=custom_oem_psm_config)
```

:::{note}
The `r` before the string in the above code section tells python to treat the
string as a sequence of literal characters. This is different behavior from
just using a regular python string. In python, strings prefixed with the `r`
are called Raw strings.  
:::

I recommend using option 1 for the best accuracy, unless you are running into
specific constraints.  For example, are using an older version of Tesseract
(less than version 4, that doesn't have the LSTM option), are running on a
system that doesn't support the LSTM (apparently some android builds), or have
performance issues.


### Page Layouts

Tesseract supports a variety of common Page Segmentation Modes.
```
0 = Orientation and script detection (OSD) only.
1 = Automatic page segmentation with OSD.
2 = Automatic page segmentation, but no OSD, or OCR. (not implemented)
3 = Fully automatic page segmentation, but no OSD. (Default)
4 = Assume a single column of text of variable sizes.
5 = Assume a single uniform block of vertically aligned text.
6 = Assume a single uniform block of text.
7 = Treat the image as a single text line.
8 = Treat the image as a single word.
9 = Treat the image as a single word in a circle.
10 = Treat the image as a single character.
11 = Sparse text. Find as much text as possible in no particular order.
12 = Sparse text with OSD.
13 = Raw line. Treat the image as a single text line,
     bypassing hacks that are Tesseract-specific.
```

Just like with the OCR engine mode, we set the Page Segmentation Mode as part
of the config string.

#### Automatic page segmentation

By default, tesseract will attempt to automatically detect the text layout. If
we have prior knowledge its best to specify the layout that is most
appropriate.

```{code-cell}
custom_oem_psm_config = r'--oem 1 --psm 3'
pytesseract.image_to_string(two_column_img, config=custom_oem_psm_config)
```

Notice that with the default page segmentation mode (fully automatic) it 
correctly identifies that the lines of text are split between the two columns
on the page.

#### Other psm options

Automatic page segmentation might not always be the best option. There are
cases when we want to use a different page segmentation mode. One consideration
is performance, tesseract will be able to run significantly faster on each
image with modes that don't require it to estimate a layout. While this is
probably not a big consideration when working with a single image, it adds up
when working over hundreds or thousands! Additionally, the automatic page
layout detection may give results that don't match your expectations. A common
case where its best to explicitly provide a layout option is when working with
tabular data.

#### Tables

Lets look at a common failure of automatic page segmentation. This image
contains the table of contents page, where the chapters are aligned to the left
and the page numbers to the right. Automatic page segmentation will separate
this into two distinct regions, grouping all the left aligned text together and
then all of the right aligned text.  

```{code-cell}
custom_oem_psm_config = r'--oem 1 --psm 3' 
pytesseract.image_to_string(toc_img, config=custom_oem_psm_config) 
```

If no segmentation mode exactly matches what you are looking for in terms of
grouping together text on the page, you can manually reconstruct the content
by using the positional data output by tesseract.


### Output Formats

So far, we have just been extracting the text as a python string. However,
tesseract, and pytesseract, support a variety of output options. Some of these
options contain more information than can be stored in just a string. This
means we can use that information to get more use out of our ocr results in
some cases. Additionally, these output formats can often be interpreted by
other software, or may be useful in some bigger pipeline such as a web app.

Pytesseract supports the following output formats which can be specified by the
function.

| Output    | Function               | Return Type    | Description | 
| --------- | ---------------------- | -----          | ----------- |
| string    | `image_to_string`      | str            | the extracted text
| osd       | `image_to_osd`         | str            | orientation and script as detected by tesseract
| boxes     | `image_to_boxes`       | str            | bounding boxes for each character 
| data      | `image_to_data`        | str/tsv or df  | tab separated table with boxes, confidences, line numbers
| alto xml  | `image_to_alto_xml`    | str/xml        | [ALTO XML](https://en.wikipedia.org/wiki/ALTO_(XML)) - standard for representing OCR and layout data in xml
| pdf       | `image_to_pdf_or_hocr` | binary/pdf     | Searchable PDF
| hocr      | `image_to_pdf_or_hocr` | str/hocr       | [hOCR](https://en.wikipedia.org/wiki/HOCR) - Another standard for representing OCR data as valid html

::: {note}
Each of these functions accept the `lang` and `config` parameters we have
already seen. Some have additional parameters, such as `image_to_data` which
accepts an `output_type` parameter which we will use later. 

In an interactive python session, we can read documentation for functions with
the `help` function. See DataLab's [introductory python reader
section](https://ucdavisdatalab.github.io/workshop_python_basics/chapters/01_python-basics.html#getting-help)
for more information.  In this case, we can see the parameters for the
different pytesseract functions with:

```
help(pytesseract.image_to_alto_xml)
```
:::

::: {tip}
To save these outputs to disk we can use python [file objects](https://docs.python.org/3/tutorial/inputoutput.html#reading-and-writing-files).

For example when working with pdfs:
```
pdfdata = pytesseract.image_to_pdf_or_hocr('img.png', extension='pdf')
with open('output.pdf', 'w+b') as f:
    f.write(pdfdata)
```

Another example saving the output in ALTO xml format:
```
xml = pytesseract.image_to_alto_xml('img.png')
with open('output.xml', 'w') as f:
    f.write(xml)
```
:::

#### Verbose OCR Data

Pytesseract's `image_to_data` function provides word level data of the ocr
output. Parsing this information can be useful for many types of analyses. By
default, the return value is a string containing a table of tab seperated
values. However, when [pandas](https://pandas.pydata.org/) is loaded, we can
have `image_to_data` return a `pandas` **DataFrame** object by setting the
`output_type` parameter:

```{code-cell}
data = pytesseract.image_to_data(toc_img, config=custom_oem_psm_config, output_type='data.frame')
data.head()
```

::: {note}
Pandas Dataframes are incredibly powerful for data analysis. For an introduction
to Pandas and dataframes see DataLab's Python Basics [reader](https://ucdavisdatalab.github.io/workshop_python_basics/chapters/02_pandas-basics.html#dataframes).
:::

Here is a summary of each column in this table. Adapted from [this blog post](https://blog.tomrochette.com/tesseract-tsv-format)

| Column    | Description                                                                                |
|-----------|--------------------------------------------------------------------------------------------|
| level     | 1: page, 2: block, 3: paragraph, 4: line, 5: word                                          |
| page_num  | starts at 1, indicates page, only useful for multi-page documents                          |
| block_num | starts at 0, pages > blocks > paragraphs > lines > words                                   |
| par_num   | starts at 0                                                                                |
| line_num  | starts at 0                                                                                |
| word_num  | starts at 0                                                                                |
| left      | x coordinate in pixels, top left corner of the bounding box, from top left corner of image |
| top       | y coordinate in pixels, top left corner of the bounding box, from top left corner of image |
| width     | width in pixels of bounding box                                                            |
| height    | height in pixels of bounding box                                                           |
| conf      | confidence value for the word, 0-100, -1 for any row that isn't a word                     |
| text      | detected word, NaN or empty for any row that isn't a word                                  |

A **bounding box** refers to a rectangular region within the image. Bounding
boxes can be used to represent a page, a block, a paragraph, a line, a word or
even a character. 

Analysis of this data can be very useful for projects that rely on the **layout**
of the documents. One use of this data is to quickly classify types of pages
within your document set, for example, you could develop heuristics for
detecting if a page contains a table of contents and filter those out. You
could use this data for extracting Titles or Headers other sequences of text
that have differing text heights. Additionally, if you only care about the text
within a certain region of the page, for example the main article body of a
journal article, you could filter out the rows that aren't within that region. 

In addition to information about the layout, this table contains the
**confidence values** associated with each word of detected text. These scores
range from 0-100 and reflect the engine's confidence in the detected word.

## Assessing Accuracy

OCR is a very challenging problem, and while current tools are very advanced and built using the latest technologies, they are imperfect. 

### Confidence scores

One quantitative way of evaluating the OCR's performance is by analyzing the **confidence values** returned from `pytesseract.image_to_data`. With Pandas we can compute some summary statistics on the values.

```{code-cell}
data["conf"].loc[data["text"].notna()].describe()
```

We can also sort the words by their confidence scores

```{code-cell}
data.sort_values('conf', ascending=False)[["text", "conf"]].loc[data["text"].notna()]
```

### Vocabulary

```{code-cell}
data["text"].value_counts()
```

## Image Preprocessing

### Working with Pdfs

### Potential obstacles
- image quality too low (300 dpi)
- skewed image
- text too small
- high pass filter before binarization
- rotation
- borders
- extraneous details on page
- weird font
- noise in paper or image

### GUI image editing software

### Options for programatically applying these fixes

imagemagick

opencv python

## Text Cleaning

## Workflow

- iterative
- look at data
- use quantitative validation metrics
- develop preprocessing strategy
- develop text cleaning strategy
