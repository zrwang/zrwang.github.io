---
layout: post
title: Setting Up a Nice Environment for LaTex on MacOS
description: This post demonstrates how to configure a nice LaTex working environment with Emacs on MacOS.
---

This post demonstrates how to configure a nice LaTex working environment with Emacs on MacOS.

## Prerequistite
- Install [MacTex](MacTeX).
- Install [Emacs](https://www.gnu.org/software/emacs/).
- Install [Skim](http://skim-app.sourceforge.net/).

## Install AucTex Package for Emacs
AucTex can be easily installed by using the Emacs package manager.

```
M-x list-packages RET
```
Mark the AucTex package for installation with `i`, and execute the installation by hitting `x`. If you encounter any issue during the previous step, please check your configuration for the [package manager](https://www.emacswiki.org/emacs/ELPA) in your [init file](https://www.emacswiki.org/emacs/InitFile#init_file).

Since we are using latexmkrc file to control the compilation of the source LaTex document into the final typeset PDF file, we have to install another package called auctex-latexmk, which adds latexmk support to AucTex package.

```
M-x package-install RET auctex-latexmk RET
```

## Configure your Emacs
Once you finish installing the packages mentioned above, you are ready to configure your Emacs by putting following configurations into your emacs init file.

```
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
;; setting up latex mode
;; Forward/inverse search with evince using D-bus.
;; Installation:
;; M-x package-install RET auctex RET
;; Tells emacs where to find LaTeX.
(let ((my-path (expand-file-name "/usr/local/bin:/usr/local/texlive/2015/bin/x86_64-darwin")))
(setenv "PATH" (concat my-path ":" (getenv "PATH")))
(add-to-list 'exec-path my-path)) 

;; AucTeX settings
(setq TeX-PDF-mode t)

(add-hook 'LaTeX-mode-hook
(lambda ()
  (push
   '("latexmk" "latexmk -pdf %s" TeX-run-TeX nil t
     :help "Run latexmk on file")
    TeX-command-list)))
(add-hook 'TeX-mode-hook '(lambda () (setq TeX-command-default "latexmk")))

(setq TeX-view-program-selection '((output-pdf "PDF Viewer")))
(setq TeX-view-program-list
      '(("PDF Viewer" "/Applications/Skim.app/Contents/SharedSupport/displayline -b -g %n %o %b")))

(custom-set-variables
     '(TeX-source-correlate-method 'synctex)
     '(TeX-source-correlate-mode t)
     '(TeX-source-correlate-start-server t))
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
```

## Configure latexmkrc file
Create a `~/.latexmkrc` with the following configuration.

```
#
# Generel
#

# Needed for the dot2texi package which invokes GraphViz.
$latex = 'latex --shell-escape';
$pdflatex = 'pdflatex --shell-escape';

# 
# Mac OS
#
$pdf_previewer = "open -a Skim";
$clean_ext = "paux lox pdfsync out";
```

## Configure your PDF viewer
If you are using Ubuntu, you don't need to do anything since the built-in PDF viewer Evince is working pretty well. If you are using MacOS, it's also pretty easy to configure the Skim with as follows.

```
Open Skim -> Preferences -> Sync
Preset: Custom
Command: /usr/local/bin/emacsclient
Arguments: --no-wait +%line "%file"
```

## Finally
Congratulations! You're all set. Open a `.tex` file and give it a try. Once you finish editing the source file, you can compile the project with `C-c C-c` and `C-c C-c` again to view the PDF in Skim. Forward search can be called by `C-c C-v` and inverse search can be called by `shift+cmd+(left click)`. 

Thanks for reading!
