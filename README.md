![Version](https://img.shields.io/static/v1?label=matplotlib-voice-in&message=0.0&color=brightcolor)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)

# NIH grant templates in org-mode

## Problem addressed
NIH grant applicants have to upload several documents to the ASSIST webservice.
For a given application, there are online forms and the uploaded documents.
The attached documents must be rendered in PDF format.
The origin of these PDFs is not essential.

Most applicants use Microsoft Word to generate these documents.
Microsoft Word is notorious for misplacing figures and tables that are text-wrapped.
The research strategy document contains numerous figures and tables that are wrapped.

One is often working on this document up to the last minute, so it is extremely frustrating when your figures have jumped out of their proper positions a half hour before the grant application has to be submitted.
Org-mode provides access to the wrapfigs package in LaTeX.
This is not a silver bullet, but it is less frustrating to work with than manually placing figures in MS Word documents.
You tweak parameter settings in the master org file to adjust the placement and space allocated for a text-wrapped figure.
Unfortunately, there can still be unexpected side effects; however, I generally find this approach to be saner than working in MS Word.

## Why Org-mode?
The principal advantages of org-mode over \LaTeX are that or both have support for working with tables that is more flexible than in LaTeX.
Org-mode supports the use of interactive computing, although such a feature would unlikely ever be utilized in the grant application.
The approach taken here could be replicated in \LaTeX.
I have done so for those visitors who are already familiar with \LaTeX and have not yet explored org-mode.

## Requirements
Because you are not using org-babel, you can assemble this or the document in VS Code using the org-mode package.
However, org-mode is best run inside of Emacs.
Org-mode is now built into GNU Emacs.
GNU Emacs is available for free, accompanied by simple installers for various computing platforms.

In principle, you can utilize the code provided here without further customization by editing the init file.el file.
You can utilize the org-cite package, which is built into Emacs, to insert citations and generate the bibliography.
I have provided a variant of the header and header snippets that utilize org-cite instead of citar.
For those who want to use the citar package for bibliography management, I have provided my configuration.

You will also need to have texlive installed.
This is the system that is used to generate PDFs from LaTeX documents.
You want to install the full version to be able to handle all kinds of edge cases that 1 typically encounters when writing about doing science.
We call upon some packages that are not in the base distribution, so it is prudent to get the full distribution to avoid the hassle of finding and installing individual packages.

For the Mac, you can install texlive via MacPorts or Homebrew.
There is also a MacTeX binary that supports a drop-and-drag approach.
Be aware that this software requires approximately 1 gigabyte of hard disk space.

### Check which texlive distribution is called by Emacs
I have included the code required to point Emacs to the texlive distribution that you want to use.
You should be aware that the R statistical package includes a stripped-down version of TeX Live.
Depending on how your path is set up, the R version of texlive could be utilized by Emacs rather than the full version of texlive.


## Approach taken

### File configuration with header
I developed a header that addresses the need to somebody using org-mode to prepare a NIH grant application.
I provide on the first lines critical metadata that you will find yourself re-entering numerous times, like the application identifier number.
My institution has its application identifier number, so I include that too.
Having this metadata available in such a handy way reduces the time spent trying to look them up again.
Below these comments, I have an org-mode drawer that contains many lines of latex statements.
This section is equivalent to the preamble in a LaTeX document.
I have included the templates for all the various kinds of documents that I have had to prepare.

An alternative approach is to add this header information to your files by using a massive snippet.
I have provided such a generic header snippet for the yasnippets package.
I have also provided headers, along with a bit of template information, for each of the specific types of forms that you need to upload.


### PMC and PCMID

I then attempted to address the issue of adding PMC and PMID identifiers to the BibTeX entries.
This is a luxury.
It would be ideal to export these two fields because they are part of the NIH's standard BibTeX style. 
I got a Python function that is supposed to be able to do this return to me by chatbot.
I fed it my entire global.bib file.
It choked on this file.

I need to run some tests on smaller bib files.
The ideal thing to do would be to extract only the cited detect entries.
I tried bibextract, a command-line tool.
I tried BibDesk, another command-line tool.
I tried the following org-orf commands: If you are using org-ref see org-ref-extract-bibtex-entries or org-ref-extract-bibtex-to-file.

There is an analog for citar.
Someone has posted such a tool on GitHubd
https://github.com/mlmbl/citar-extract-bibtex
The website has instructions for how to install the package manually.
It also has instructions on how to install it using the leaf.el package.
It does not tell you how to install it using use-package or straight.
You have to read the single package file to figure out that you want to invoke the last function in the list of functions.
The functions above the last function are called by the last function.


```bash
citar-extract-bibtex-to-file
```


### Flatten references cited document
The bibliography management is a problem when it comes to NIH grant applications because you are not allowed to have hyperlinks in the grand application. I am using citar to manage the bibliography. Of course, you could use org-ref or org-cite. All these packages will export hyperlinks in the bibliography in the PDF. 
I tried to address this issue by developing elisp functions to remove these hyperlinks before exporting to the PDF.
I later found a much simpler solution: downloading and installing the cpdf package.
Then you run the following command or source the following bash function so that you do not have to memorize this long command:

```bash
cpdf -replace-dict-entry /URI flat.pdf -replace-dict-entry-value '""' -o outflat.pdf
```

OR

```bash
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

```bash
https://github.com/mlmbl/citar-extract-bibtex.git
citar-extract-bibtex-to-file
```


### Handling of sections and subsections

Org-mode and LaTeX tend to use enlarged font sizes to distinguish different levels of sections.
In an NIH grant, this is a waste of precious space.
I utilize \subsubsection{} headlines because the font is the same height as the font in the non-bold text.
I use this at the top level.
For lower-level headlines, I use the paragraph environment or bold text.
The advantage of this approach is that it saves the white space that occupies most of the line, which headlines tend to have.

I utilize a block paragraph format with no indenting.
I utilize blank lines between paragraphs because this approach provides a document that is more inviting to read than the traditional method, which uses extreme indentation to mark different paragraphs but lacks space between them.
The traditional approach can cram in more words at the expense of creating more pain for the reader.

The reviewer is likely to be in a bad mood when they are reading your grant application. 
They are thinking in the back of their mind about all the papers that they could be working on while they are spending their time on your grant application.
They do not receive direct rewards for working on your grant application, whereas they do receive rewards if they publish papers and write their own grant applications that are funded.
You are better off making the text leaner by careful editing and freeing up a little more white space.


### Text wrapping of tables and figures

The most challenging attachment is the research strategy, which contains text-wrapped images and the bibliography.
In most document assembly systems, the bibliography is generated with the main text, so it is strange that the 2 items have to be uncoupled.
This is handled by generating the research strategy and bibliography together in a single file and exporting them to a PDF.
The PDF is opened in a PDF reader, and the research strategy pages are deleted.
There is probably a better way of handling this.






|Version      | Changes                                                                                                                                                                         | Date                 |
|:-----------|:------------------------------------------------------------------------------------------------------------------------------------------|:--------------------|
| Version 0.1 |   Added badges, funding, and update table.  Initial commit.                                                                                                                | 6/14/2025  |

## Sources of funding

- NIH: R01 CA242845
- NIH: R01 AI088011
- NIH: P30 CA225520 (PI: R. Mannel)
- NIH: P20 GM103640 and P30 GM145423 (PI: A. West)



