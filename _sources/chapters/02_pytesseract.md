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

OCR with pytesseract
====================

links:
- https://cran.r-project.org/web/packages/tesseract/index.html
- https://tesseract-ocr.github.io/tessdoc/FAQ.html#running-tesseract
- https://tesseract-ocr.github.io/tessdoc/ImproveQuality.html#page-segmentation-method
- https://pyimagesearch.com/2021/08/16/installing-tesseract-pytesseract-and-python-ocr-packages-on-your-system/
- https://guides.nyu.edu/tesseract/usage
- https://github.com/madmaze/pytesseract
- https://nanonets.com/blog/ocr-with-tesseract/

## Setup

### Importing the necessary libraries
```{code-cell}
import pytesseract

pytesseract.get_tesseract_version()
```

### Loading the images into python
An image file that has been appropriately prepared.
```{code-cell}
simple_img = './data/alice_start-gutenberg.jpg'
fr_img = './data/fr_ocr-wikipedia.png'
kor_img = './data/kor_ocr-wikipedia.png'
toc = './data/alic_toc-gutenberg.jpg'
two_column = './data/two_column-google.png'
```

#### supported image formats

pytesseract can operate on any PIL Image, NumPy array or file path of an image
than can be processed by Tessseract. Tesseract supports most image formats:
png, jpeg, tiff, bmp, gif.

Notably, pytesseract, and tesseract, don't work on Pdf files. In order to
perform OCR on a pdf file, you must first convert it to a supported image
format. See the 'Prework' section for details on how to do this.

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
text to be layed out on the page. For example it supports options for if the
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

This returns just a string of all the text detected in the image.

### Language Specification

By default, tesseract uses its english training data. This can lead to very poor results if there are non english characters in the image. This is especially true if the image contains text that doesn't use a latin alphabet.

```{code-cell}
pytesseract.image_to_string(fr_img)
```

```{code-cell}
pytesseract.image_to_string(kor_img)
```

In order to use ocr on languages other than english we need to download the language's associated training data for tesseract. See index for how to do so on your system.

With pytesseract we can see all the available languages with:
```{code-cell}
:tags: ["output_scroll"]
pytesseract.get_languages()
```

To specify the language to use, pass the name of the language as a parameter to `pytesseract.image_to_string`:
```{code-cell}
pytesseract.image_to_string(kor_img, lang='kor')
```

multiple languages
```{code-cell}
pytesseract.image_to_string(kor_img, lang='kor+eng')
```

### Engine Selection

Tesseract supports the following options for selecting the engine:
```
0 = Original Tesseract only.
1 = Neural nets LSTM only.
2 = Tesseract + LSTM.
3 = Default, based on what is available.
```

I recommend using option 1. The default seems to use option 2. 

To set the 'oem' (OCR engine mode) with pytesseract we pass it as the 'config' parameter:
```{code-cell}
custom_oem_psm_config = r'--oem 1'
pytesseract.image_to_string(simple_img, config=custom_oem_psm_config)
```

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

Just like with the OCR engine mode, we set the Page Segmentation Mode as part of the config string.

#### Auto page segmentation

By default, tesseract will attempt to automatically detect the text layout. If
we have prior knowledge its best to specify the layout that is most
appropriate.

```{code-cell}
custom_oem_psm_config = r'--oem 1 --psm 3'
pytesseract.image_to_string(two_column, config=custom_oem_psm_config)
```

Notice that with the default page segmentation mode (fully automatic) it 
correctly identifies that the lines of text are split between the two columns
on the page.

If we were to use a psm that 


#### Uniform block
--psm 6

#### Tables

--psm 4


### Output Formats

```
string
boxes
data
osd
alto xml
```

#### string

#### data


## Validation

### Confidence scores

### Summary statistics

### Vocabulary
