![Version](https://img.shields.io/static/v1?label=nih-grant-orgmode&message=0.1.1&color=brightcolor)
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

One is often working on this document up to the last minute, so it is highly frustrating when your figures have jumped out of their proper positions a half hour before the grant application has to be submitted.
Org-mode provides access to the wrapfigs package in LaTeX.
This is not a silver bullet, but it is less frustrating to work with than manually placing figures in MS Word documents.
You tweak parameter settings in the master org file to adjust the placement and space allocated for a text-wrapped figure.
Unfortunately, there can still be unexpected side effects; however, I generally find this approach to be saner than working in MS Word.

## Why Org-mode?
The principal advantage of Org-mode over LaTeX is that Org-mode has more flexible support for working with tables than LaTeX.
Org-mode supports the use of interactive computing, although such a feature would unlikely ever be utilized in the grant application.
The approach taken here could be replicated in LaTeX.
I have done so for those visitors who are already familiar with \LaTeX and have not yet explored org-mode.

## Requirements
Because you are not using org-babel, you can assemble this or the document in VS Code using the org-mode package.
However, org-mode is best run inside of Emacs.
Org-mode is now built into GNU Emacs.
GNU Emacs is available for free, accompanied by simple installers for various computing platforms.

You can use the code provided here without adding more customization by editing your *init.el* file.
You can utilize the *org-cite* package, which is built into Emacs, to insert citations and generate the bibliography.
I plan to provide a variant of the header and header snippets that utilize org-cite instead of citar.
For those who want to use the citar package for bibliography management, I have provided my configuration below:

```elisp
(straight-use-package 'citar)  
(straight-use-package 'citar-org-roam)  

(require 'citar)  
(require 'oc)  
(require 'citar-org-roam)  

;; Set bibliography  
(setq citar-bibliography '("~/Documents/global.bib"))  

;; Set up org-cite processors  
(setq org-cite-global-bibliography citar-bibliography)  
(setq org-cite-insert-processor 'citar)  
(setq org-cite-follow-processor 'citar)  
(setq org-cite-activate-processor 'citar)  

;; Configure actions  
(setq citar-open-actions   
      '(("note" . citar-org-roam--create-capture-note)  
        ("pdf" . citar-file-open-external)))  

;; Add keybindings  
(global-set-key (kbd "C-c b") 'citar-insert-citation)  
(global-set-key (kbd "C-c o") 'citar-open)  

;; Templates with added note template  
(setq citar-templates  
      (list   
       (cons 'main "${author:30}     ${date year:4}     ${title:48}")  
       (cons 'suffix "          ${=key= id:15}    ${=type=:12}    ${tags keywords:*}")  
       (cons 'preview "${author:30}     ${date year:4}     ${title:48}")  
       (cons 'note "${title:*}")))  

;; Configure citation appearance  
(custom-set-faces  
 '(org-cite ((t (:foreground "RoyalBlue" :inherit org-link))))  
 '(org-cite-key ((t (:foreground "RoyalBlue" :inherit org-link)))))  

;; Enable tooltips for citations  
(setq citar-org-citation-tooltip t)  
(setq citar-org-show-citation-on-follow t)  

;; Configure what information appears in tooltips  
(setq citar-citation-info-template   
      '(("author" . "${author}")  
        ("year" . "${date year}")  
        ("title" . "${title}")  
        ("journal" . "${journal}")))  

;; Corrected indicators format  
(setq citar-symbols  
      `((file . "📄")  
        (note        . "📝")  
        (link . "🔗")))  
```

You will also need to have *texlive* installed.
This is the collection of libraries and programs that deploy LaTeX.
This system is used to generate PDFs from LaTeX documents.
You want to install the full version to be able to handle all kinds of edge cases that one typically encounters when writing about science.
We call upon some packages that are not in the base distribution, so it is prudent to get the full distribution to avoid the hassle of finding and installing individual packages.

For the Mac, you can install *texlive* via MacPorts or Homebrew.
There is also a *MacTeX* binary that supports a drag-and-drop approach to installation.
This software requires over 1 gigabyte of hard disk space.

### Check which *texlive* distribution is called by Emacs
I have included the code required to point Emacs to the *texlive* distribution that you want to use.
You should be aware that the R statistical package includes a stripped-down version of *textlive* that is missing too many packages to bother using.
Depending on how your PATH is defined in your `.bashrc` or `.zshrc file`, the R version of *texlive* could be utilized by Emacs rather than the full version of *texlive*.
The file path that comes first in the PATH variable is the one that is used.


## Approach taken

### File configuration with header
I developed a header that addresses my needs for using org-mode to prepare a NIH grant application.
I provide, on the first lines, critical metadata that you will find yourself re-entering numerous times, like the application identifier number.
My institution has another application identifier number, so I have included that as well.
Having this metadata available in such a handy way reduces the time spent looking it up again.
Below these comments, I have an org-mode drawer that contains many lines of LaTeX statements.
This section is equivalent to the preamble in a LaTeX document.
I have included the templates for all the various kinds of documents that I have had to prepare.

An alternative approach is to add this header information to your files by using a massive snippet.
I have provided such a generic header snippet for the yasnippets package.
I have also provided headers, along with template information, for each of the specific types of forms that you need to upload.


### PMC and PCMID

I then attempted to address the issue of adding PMC and PMID identifiers to the BibTeX entries.
This is a luxury.
It would be ideal to export these two fields because they are part of the NIH's standard BibTeX style.
Some articles are not in PubMed and lack these fields.
Some articles have a PMC but not a PMCID because they were not funded by the NIH.


### Extracting cited files

The ideal approach would be to extract from a global.bib file the cited BibTeX entries and save these in a separate BibTeX file.
There are several methods for performing this extraction.

- bibextract, a command-line tool.
- BibDesk, another command-line tool.
- bibfish
- In Emacs, when using org-ref for managing citations:  org-ref-extract-bibtex-entries or org-ref-extract-bibtex-to-file.
- In emacs, when using citar for managing citations: [citar-extract-bibtex-to-file](https://github.com/mlmbl/citar-extract-bibtex).
  



### Flatten references cited document
Bibliography management is a problem when it comes to NIH grant applications because hyperlinks are not allowed in the grant application. 
I am using *citar* to manage the bibliography. 
Of course, you could use *org-ref* or *org-cite*. 
All these packages will export hyperlinks in the bibliography in the PDF. 
I attempted to address this issue by developing Elisp functions to remove these hyperlinks before exporting to PDF.

I later found a much simpler solution: downloading and installing the Coherent PDF command-line tool, [cpdf](https://community.coherentpdf.com/).
Then you can run the following command:

```bash
cpdf -replace-dict-entry /URI flat.pdf -replace-dict-entry-value '""' -o outflat.pdf
```

Alternatively, you can source the following bash (or zsh) function, so you do not have to memorize this lengthy command.

```bash
flatpdf(){
echo "Function to flatten hyperlinks in a PDF used in a NIH grant application. Takes the prefix or stem of the filenames."
if [ $# -lt 1 ]; then
   echo 1>&1 "$0: not enough arguments"
   echo "Usage: flatpdf <input filestem>"
   return 2
elif [ $# -gt 1 ]; then
   echo 1>&1 "$0: too many arguments"
   echo "Usage: flatpdf <input filestem>"
   return 2
fi
cpdf -replace-dict-entry /URI ${1:input}.pdf -replace-dict-entry-value '""' -o ${1:input}Flattened.pdf
}
```

### Extract the cited BibTeX entries

```bash
https://github.com/mlmbl/citar-extract-bibtex.git
citar-extract-bibtex-to-file
```


### Handling of sections and subsections

Org-mode and LaTeX tend to use enlarged font sizes to distinguish different levels of sections.
They can also display numbers for this purpose, but I find these to be a form of clutter.
In an NIH grant, this is a waste of precious space.
I utilize `\subsubsection*{}` headlines because the font is the same height as the font in the non-bold text.
I use this at the top level.
For lower-level headlines, I use the `\paragraph*` environment or bold text.
The advantage of this approach is that it saves the white space that occupies most of the line, which headlines tend to have.

I utilize a block paragraph format with no indenting.
I utilize blank lines between paragraphs because this approach provides a document that is more inviting to read than the traditional method, which uses extreme indentation to mark different paragraphs but lacks space between paragraphs.
The traditional approach can cram in more words at the expense of creating more pain for the reader.
You are better off making the text leaner by careful editing and freeing up a little more white space.


### Text wrapping of tables and figures

The most challenging attachment is the Research Strategy, which contains text-wrapped images and the bibliography.
In most document assembly systems, the bibliography is generated in conjunction with the main text; therefore, it is unusual for these two items to be uncoupled.
This is handled by generating the Research Strategy and bibliography together in a single file and exporting them to a PDF.
The PDF is opened in a PDF reader, and the research strategy pages are deleted to leave the bibliography, which is then saved to a separate PDF file.
There may be a better way of handling this.



|Version      | Changes                                                                                                                                                                         | Date                 |
|:-----------|:------------------------------------------------------------------------------------------------------------------------------------------|:--------------------|
| Version 0.1 |   Added badges, funding, and update table.  Initial commit.                                                                                                                | 6/14/2025  |
| Version 0.1.1 |  Edits of the README.md to improve clarity.                                                                                                              | 6/15/2025  |


## Sources of funding

- NIH: R01 CA242845
- NIH: R01 AI088011
- NIH: P30 CA225520 (PI: R. Mannel)
- NIH: P20 GM103640 and P30 GM145423 (PI: A. West)



