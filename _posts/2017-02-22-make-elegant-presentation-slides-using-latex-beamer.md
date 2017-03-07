---
layout: post
title: Making Elegant Presentation Slides Using LaTex Beamer
description: This post demonstrates how to make elegant presentation slides using LaTex with Beamer package.
---

This post demonstrates how to make elegant presentation slides using LaTex with Beamer package. If you are an Emacs user and haven't read my previous posts about how to set up a nice LaTex working environment with Emacs on [MacOS](/2017/02/20/setting-up-a-nice-environment-for-latex-on-macos/) and [Ubuntu](/2017/02/21/setting-up-a-nice-environment-for-latex-on-ubuntu/), please have a look at it and I am pretty sure you're gonna love it.

## What's Beamer
[Beamer](https://www.ctan.org/pkg/beamer?lang=en) is a LaTeX document class for creating slides for presentations. With Beamer, it's going to be much easier for us to create presentation slides using LaTex. Next, let me show you how to make elegant slides from scratch.

## Create a LaTex Project
Fire a terminal and execute the following commands.

```shell
cd ~
mkdir -p example/pdf
cd example
mkdir bib
touch main.tex
```

In this example project, we create a folder `example` with two sub-folders `pdf` and `bib` in it. `pdf` contains all the figures you are including in the slides (we use pdf format for all the figures), and `bib` stores bibliography related files. The main source file `main.tex` is created in the root directory. Put the logo figures in the `pdf` sub-folder, and you should have a similar file tree as follows.

```shell
example/
├── bib
├── main.tex
└── pdf
    ├── CUHK_logo.pdf
    └── JHU_logo.pdf
```

Fire a text editor that you are comfortable with (e.g., Emacs). Copy and paste the following codes into the newly created `main.tex` file.

```latex
\documentclass[serif]{beamer}

% you can play with different themes and color themes to find your favorite combination.
\mode<presentation> {
  \usetheme{Luebeck}
  \usecolortheme{beaver}
  \beamertemplatenavigationsymbolsempty
  \setbeamertemplate{headline}{}
}

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
% include necessary packages here
\usepackage{graphicx} % for including images
\graphicspath{ {./pdf/} } % declare the path where your graphic files are
\DeclareGraphicsExtensions{.pdf} % so you won't have to specify these with every instance of \includegraphics
\usepackage{pgf} % for logo
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
% declare new command here
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

% ----------------------------------------------------------
%	TITLE PAGE
% ----------------------------------------------------------

% The short title in [] appears at the bottom of every slide
% The full title is only on the title page
\title[short title \hspace{25mm}\insertframenumber/\inserttotalframenumber]{
Title
}

\author{Your name}

\institute{
  Your institution
  \medskip
  \textit{Your email address}
}

\date{\today} % Date, can be changed to a custom date

\logo{
  \pgfputat{\pgfxy(0,7.8)}{\pgfbox[right,base]
    {\includegraphics[width=1.2cm,height=1.2cm,keepaspectratio]{CUHK_logo}
      \hspace{\dimexpr\paperwidth-2.4cm-5pt}
      \includegraphics[width=.9cm,height=.9cm,keepaspectratio]{JHU_logo}}}
}

\begin{document}

\begin{frame}
  \titlepage % Print the title page as the first slide
\end{frame}

\logo{} % logo only appears on the first slide

% Table of contents slide, comment this block out to remove it
\begin{frame}
  \frametitle{Table of Contents}
  \fontsize{12}{10}
  \tableofcontents
\end{frame}

% Throughout your presentation, if you choose to use \section{} and \subsection{} commands, these will automatically be printed on this slide as an overview of your presentation
\AtBeginSection[]{
  \begin{frame}
    \frametitle{Table of Contents}
    \fontsize{12}{10}
    \tableofcontents[
    currentsection,
    sectionstyle=show/shaded,
    subsectionstyle=show/show/shaded,
    ]
  \end{frame}
}

\end{document}
```

Compile the project and you will see a PDF file with the following pages.

<p class="full-width">
<img src="/public/image/2017-02-22/example_1_1.png" width="70%" align="right"/>
</p>

<p class="full-width">
<img src="/public/image/2017-02-22/example_1_2.png" width="70%" align="right"/>
</p>

The second page is empty because we haven't defined any sections or subsections. So let's add more slides.

Include two more useful packages `subfig` and `bm`, and add more slides by copying the `presentation slides code` at the end of previous code block.

```latex
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
% include necessary packages here
\usepackage{graphicx} % for including images
\graphicspath{ {./pdf/} } % declare the path where your graphic files are
\DeclareGraphicsExtensions{.pdf} % so you won't have to specify these with every instance of \includegraphics
\usepackage{pgf} % for logo
\usepackage{subfig}
\usepackage{bm}
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

% ----------------------------------------------------------
%	PRESENTATION SLIDES
% ----------------------------------------------------------

\section{section A}

\subsection{subsection B}
\begin{frame}\frametitle{title D}
  \begin{figure}
    \centering
    \includegraphics[height=.7\textheight]{example-image}
    \caption{This is the caption.}
  \end{figure}
\end{frame}

\section{section C}

\subsection{subsection D}
\begin{frame}\frametitle{title E}
  \begin{figure}
    \centering
    \subfloat[First subfigure\label{fig:a}]{\includegraphics[height=2cm,width=3cm]{example-image}}\qquad
    \subfloat[Second subfigure\label{fig:b}]{\includegraphics[height=2cm,width=3cm]{example-image-a}}
    \caption{A figure}
    \label{fig:1}
  \end{figure}
  \begin{figure}
    \centering
    \subfloat[Third subfigure\label{fig:c}]{\includegraphics[height=2cm,width=3cm]{example-image-b}}\qquad    
    \subfloat[Fourth subfigure\label{fig:d}]{\includegraphics[height=2cm,width=3cm]{example-image-c}}
    \caption{Another figure}
    \label{fig:2}
  \end{figure}
\end{frame}

\begin{frame}\frametitle{title F}
  \begin{align}
    \mathbf{H}(\bm{\theta}(t)) \ddot{\bm{\theta}}(t) + \mathbf{C}(\bm{\theta}(t),\dot{\bm{\theta}}(t)) + \mathbf{G}(\bm{\theta}(t)) = \bm{\tau}
  \end{align}
\end{frame}
```
After compilation, you can obtain some elegant slides as follows.

<p class="full-width">
<img src="/public/image/2017-02-22/example_2_1.png" width="70%" align="right"/>
</p>

<p class="full-width">
<img src="/public/image/2017-02-22/example_2_2.png" width="70%" align="right"/>
</p>

<p class="full-width">
<img src="/public/image/2017-02-22/example_2_3.png" width="70%" align="right"/>
</p>

<p class="full-width">
<img src="/public/image/2017-02-22/example_2_4.png" width="70%" align="right"/>
</p>

<p class="full-width">
<img src="/public/image/2017-02-22/example_2_5.png" width="70%" align="right"/>
</p>

<p class="full-width">
<img src="/public/image/2017-02-22/example_2_6.png" width="70%" align="right"/>
</p>

<p class="full-width">
<img src="/public/image/2017-02-22/example_2_7.png" width="70%" align="right"/>
</p>

Enjoy playing with Beamer.

Thanks for reading!
