---
layout: post
title: Setting Up a Nice Environment for LaTex on Ubuntu
---

This post is similar with the previous [one](/2017/02/21/setting-up-a-nice-environment-for-latex-on-macos/) for MacOS, and this one demonstrates how to configure a nice LaTex working environment with Emacs on Ubuntu.

## Prerequisites
- Install [Tex Live](http://www.tug.org/texlive).
- Install [Emacs](https://www.gnu.org/software/emacs/).

## Install AucTex Package for Emacs
AucTex can be easily installed by using the Emacs package manager.

```
M-x list-packages RET
```
Mark the AucTex package for installation with `i`, and execute the installation by hitting `x`. If you encounter any issue during the previous step, please check your configuration for the [package manager](https://www.emacswiki.org/emacs/ELPA) in your [init file](https://www.emacswiki.org/emacs/InitFile#init_file).

## Configure your Emacs
Once you finish installing the packages mentioned above, you are ready to configure your Emacs by putting following configurations into your emacs init file.

```
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
;; setting up latex mode
;; Forward/inverse search with evince using D-bus.
;; Installation:
;; M-x package-install RET auctex RET
(add-hook 'LaTeX-mode-hook 'TeX-PDF-mode)
(add-hook 'LaTeX-mode-hook 'TeX-source-correlate-mode)
(setq TeX-source-correlate-method 'synctex)

(if (require 'dbus "dbus" t)
    (progn
      ;; universal time, need by evince
      (defun utime ()
	(let ((high (nth 0 (current-time)))
	      (low (nth 1 (current-time))))
	  (+ (* high (lsh 1 16) ) low)))

      ;; Forward search.
      ;; Adapted from http://dud.inf.tu-dresden.de/~ben/evince_synctex.tar.gz
      (defun auctex-evince-forward-sync (pdffile texfile line)
	(let ((dbus-name
	       (dbus-call-method :session
				 "org.gnome.evince.Daemon"  ; service
				 "/org/gnome/evince/Daemon" ; path
				 "org.gnome.evince.Daemon"  ; interface
				 "FindDocument"
				 (concat "file://" pdffile)
				 t     ; Open a new window if the file is not opened.
				 )))
	  (dbus-call-method :session
			    dbus-name
			    "/org/gnome/evince/Window/0"
			    "org.gnome.evince.Window"
			    "SyncView"
			    texfile
			    (list :struct :int32 line :int32 1)
			    (utime))))

      (defun auctex-evince-view ()
	(let ((pdf (file-truename (concat default-directory
					  (TeX-master-file (TeX-output-extension)))))
	      (tex (buffer-file-name))
	      (line (line-number-at-pos)))
	  (auctex-evince-forward-sync pdf tex line)))

      ;; New view entry: Evince via D-bus.
      (setq TeX-view-program-list '())
      (add-to-list 'TeX-view-program-list
		   '("EvinceDbus" auctex-evince-view))

      ;; Prepend Evince via D-bus to program selection list
      ;; overriding other settings for PDF viewing.
      (setq TeX-view-program-selection '())
      (add-to-list 'TeX-view-program-selection
		   '(output-pdf "EvinceDbus"))

      ;; Inverse search.
      ;; Adapted from: http://www.mail-archive.com/auctex@gnu.org/msg04175.html
      (defun auctex-evince-inverse-sync (file linecol timestamp)
	(let ((buf (get-file-buffer (substring file 7)))
	      (line (car linecol))
	      (col (cadr linecol)))
	  (if (null buf)
	      (message "Sorry, %s is not opened..." file)
	    (switch-to-buffer buf)
	    (goto-line (car linecol))
	    (unless (= col -1)
	      (move-to-column col)))))

      (dbus-register-signal
       :session nil "/org/gnome/evince/Window/0"
       "org.gnome.evince.Window" "SyncSource"
       'auctex-evince-inverse-sync)))
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
```
## Finally
Yes! That's it. You're all set. Open a `.tex` file and give it a try. Once you finish editing the source file, you can compile the project with `C-c C-c` and `C-c C-c` again to view the PDF in Evince. Forward search can be called by `C-c C-v` and inverse search can be called by `ctrl+(left click)`. 

Thanks for reading!

