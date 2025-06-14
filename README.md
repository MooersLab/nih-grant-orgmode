![Version](https://img.shields.io/static/v1?label=matplotlib-voice-in&message=0.0&color=brightcolor)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)

# NIH grant templates in org-mode

## Problem addressed
NIH grant applicants have to upload a number of documents to the ASSIST webservice.
For a given application, there are online forms and the uploaded documents.
The attached documents have to be rendered in PDF.
The origin of these PDFs is not important.

Most applicants use Microsoft Word to generate these documents.
Microsoft Word is notorious for mangling the placement of figures and tables that are text wrapped.
The research strategy document contains many wrapped figures and tables.

One is often working on this document up to the last minute, so it is extremely frustrating when your figures have jumped out of their proper positions a half hour before grant application has to be submitted.
Org-mode provides access to the wrapfigs package in LaTeX.
This is not a silver bullet, but it seems to be less frustrating to work with than the manual placement of figures in MS Word documents.
You tweak parameter settings in the master org file to adjust the placement and space allocated for a text-wrapped figure.
Unfortunately, there can still be unexpected side effects, but generally, I find this approach to be saner than working in MS Word.

## Why Org-mode?
The principal advantages of org-mode over \LaTeX are that or both have support for working with tables that is more flexible than in LaTeX.
Org-mode supports the use of interactive computing, although such a feature would unlikely ever be utilized in the grant application.
The approach taken here could be replicated in \LaTeX.
I have done so for those visitors who are already conversant with \LaTeX and have not yet played around with org-mode.

## Requirements
Because you are not utilizing org-babel, you might be able to get this or document assembled in VS Code using the org-mode package.
However, org-mode is best run inside of Emacs.
Org-mode is now built into GNU Emacs.
GNU emacs is available for free with some simple installers for different computing platforms.

In principle, you might be able to utilize the code provided here without doing any further customization by editing the init.el file.
You could utilize the org-cite package that is built in to insert citations and generate the bibliography.
I have provided a variant of the header and headers snippets that utilizes org-cite instead of citar.
For those who want to use the citar package for bibliography management, I have provided my configuration.

You will also need to have texlive installed.
This is the system that is used to generate pdfs from latex documents.
You want to install the full version to be able to handle all kinds of edges cases that 1 typically encounters when writing about doing science.
We actually call upon some packages that are not in the base distribution so it is prudent to get the full distribution to avoid the hassle finding and installing individual packages.

For the Mac, you can install text live via macports or home brew.
There has also a MacTeX binary that supports a drop and drag approach.
Beware that this software takes up about a gigabyte of space.

I have included the code required to point Emacs to the text live distribution that you want to use.
You should beware that there are statistical package has built into it a stripped down version of texlive.
Depending on how your path is set up, the r version of texlive could be utilized by Emacs rather than the full version of texlive.


## Approach taken

### File configuration with header
I developed a header that addresses the need to somebody using org-mode to prepare a NIH grant application.
I provide on the first of all lines that are coming it out critical metadata that you will find yourself reentering numerous times, like the application identifier number.
My institution has its application identifier number, so I include that too.
Having this metadata available in such a handy way reduces time spent trying to look them up again.
Below these comments, I have an org-mode drawer that contains many lines of latex statements.
This section is equivalent to the preamble in a latex document.
I have included the templates for all the various kinds of documents that I have had to prepare.

An alternative approach is to add this header information to your files by using a massive snippet.
I have provided such a generic header snippet for the yasnippets package.
I have also provided headers plus a little template information for each of the specific kinds of forms that you have to upload.


### PMC and PCMID

I then tried to tackle the problem of adding pmc and pcmid identifiers to the bibtex entries.
This is a luxury.
It would be ideal to be able to export these 2 fields because that is part of the nihs standard BibTeX style. decentries are not required
I got a Python function that is supposed to be able to do this return to me by chatbot.
I fed it my entire global.bib file.
It choked on this file.

I need to run some tests on smaller bib files.
The ideal thing to do would be to extract only the cited detect entries.
I tried bibextract, a command line tool.
I tried bibdesk, another command line tool.
I tried the following org-orf commands: If you are using org-ref see org-ref-extract-bibtex-entries or org-ref-extract-bibtex-to-file.

I wonder if there has an analog for citar.
Yes, someone has posted such a tool on github 8 months ago.
https://github.com/mlmbl/citar-extract-bibtex
The website has instructions for how to install the package manually.
It also has instructions on how to install it using the leaf.el package.
It do not tell you how to install it using use-package or straight.
You have to read the single package file to figure out that you want to invoke the last function in the list of functions.
I think that the functions above the last function are called by the last function.


```bash
citar-extract-bibtex-to-file
```


### Flatten references cited document
The bibliography management is a problem when it comes to NIH grant applications because you are not allowed to have hyperlinks in the grand application. I am using citar to manage the bibliography. Of course, you could use org-ref or org-cite. All these packages will export hyperlinks in the bibliography in the PDF. 
I tried to address this issue by developing needless functions to remove these hyperlinks before exporting to the PDF.
I later found a much simpler solution which is to download and install the package cpdf.
Then you run the following command or source the following bash function:

```bash
cpdf -replace-dict-entry /URI flat.pdf -replace-dict-entry-value '""' -o outflat.pdf

OR

flatpdf(){
echo "Function to flatten hyperlinks in a PDF used in a NIH grant application."
if [ $# -lt 2 ]; then
   echo 1>&2 "$0: not enough arguments"
   echo "Usage: <input>.pdf <output>Flatten.pdf"
   return 2
elif [ $# -gt 2 ]; then
   echo 1>&2 "$0: too many arguments"
   echo "Usage: <input>.pdf <output>Flatten.pdf"
   return 2
fi
cpdf -replace-dict-entry /URI ${1:input}.pdf -replace-dict-entry-value '""' -o ${2:output}Flatten.pdf
}
```

### Extract the cited bibtex entries

https://github.com/mlmbl/citar-extract-bibtex.git
citar-extract-bibtex-to-file


### Handling of sections and subsections

Org-mode and LaTeX tend to use enlarged font sizes to distinguish different levels of sections.
In a nih grant this is a waste of precious space.
I utilize \subsubsection{} headlines because of font is the same height as the font in the non-bold text.
I use this at the top level.
For lower level headlines, I am using paragraph environment or just bold text.
The advantage of this approach is that this saves the white space that occupies most of the line that headlines tend to have.

I utilize a block paragraph format with no indenting.
I utilize blank lies between paragraphs because this provides a document that is more inviting to read than the traditional approach which utilizes extreme indentation to mark different paragraphs but does not have space between the paragraphs.
The traditional approach is able to cram in more words at the expense of creating more pain for the reader.

The reviewer is likely to be in a bad mood when they are reading your grant application; 
They are thinking in the back of their mind about all the papers that they could be working on while they are spending their time on your grant application.
They do not get rewarded in any direct way for working on your grandtapplication, whereas they do get rewarded if they publish papers and write their own grant applications that get funded.
You are better off making the text leaner by careful editing and freeing up a little more white space.


### Text wrapping of tables and figures

The most challenging attachments is the research strategy which contains text wrapped images and the bibliography.
In most document assembly systems, the bibliography is generated with the main text so it is strange that the 2 items have to be uncoupled.
This is handled by generating the research strategy and bibliography together in a and exporting them to a PDF.
The PDF is opened in a PDF reader and the research strategy pages are deleted.
There has probably a better way of handling this/






|Version      | Changes                                                                                                                                                                         | Date                 |
|:-----------|:------------------------------------------------------------------------------------------------------------------------------------------|:--------------------|
| Version 0.1 |   Added badges, funding, and update table.  Initial commit.                                                                                                                | 6/14/2025  |

## Sources of funding

- NIH: R01 CA242845
- NIH: R01 AI088011
- NIH: P30 CA225520 (PI: R. Mannel)
- NIH: P20 GM103640 and P30 GM145423 (PI: A. West)



