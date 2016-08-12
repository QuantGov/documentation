# The QuantGov Architecture

## Goals

QuantGov originated as as RegData, a novel dataset quantifying restrictive phrases in the *Code of Federal Regulations* and classifying according to the *North American Industry Classification System*. However, it soon became clear that the methods applied in that effort could be usefully replicated in other contexts. There are other interesting bodies of law, and there are other interesting ways to analyze text using the advances in machine learning.

The architecture used to create QuantGov, therefore, has been designed to be modular, flexible, and extensible. We want a system where it is not only possible, but straightforward to add in or swap out either collections of text or machine learning algorithms to create new and innovative projects. To encourage others to build on our system, we have decided to adopt an open-source strategy, and our code is distinctly inspired by the [Unix philosophy] of writing small programs that work together.

## Four Types of Components

There are four types of components in the QuantGov architecture:

-   A **[corpus]** represents some body of text. A corpus will clean and organize text, generate metadata that describes the text, and provides a driver that scripts can use to read through the text in an ordered way.
-   An **[estimator]** represents a classification or regression task, or some kind of unsupervised learning task. Estimators should evaluate and tune candidate algorithms, train the chosen candidate, and provide a script that can perform the estimation on a given corpus.
-   A **[project]** combines corpora and estimators together to create a dataset. Projects may perform some finishing or clean-up work on the final product.
-   A **[databank]** holds external resources not automatically obtained or created by a corpus, estimator, or project.

As an example, as of this writing the RegData project uses two corpora—one representing the CFR for analysis and one representing the Federal Register to train the classifiers—and five predictors—one for each level of NAICS classification).

## Make for Workflow Management

Corpora, estimators, and projects all use the program [Make] to manage workflow. Make allows us to write *recipes* for a *target*, and to specify *dependencies* that have to be created before the recipe can be run. Make was originally designed for compilation of large computer programs, but is a great fit for data analysis tasks for several reasons:

-   Make enables clear separation of tasks without needing to mentally keep track of what needs to happen in what order
-   Through its dependency tracking, Make only re-runs a recipe when its dependencies change, which minimizes the cost of making changes during development
-   Make is tool-agnostic; the same project can use scripts in Python, R, Java, Scala, and anything else that can be run from the command line for different steps in the analysis
-   Environment variables simplify project-wide configuration

Perhaps most importantly, Make can tie together the pieces of a project without having to hard-code information about each other. Multiple projects can use the same predictor or corpus, because the corpus and predictor do not need to know anything about each other for the final analysis to complete successfully.

The makefiles in the standard project are fully commented and can be taken as examples. The [Make manual][Make] also provides extensive documentation. Make is usually installed as part of OS X or Linux. Windows users can install it from [GnuWin32], or as part of [Gow].

  [Unix philosophy]: http://www.catb.org/esr/writings/taoup/html/ch01s06.html
  [corpus]: corpus.markdown
  [estimator]: estimator.markdown
  [project]: project.markdown
  [databank]: databank.markdown
  [Make]: https://www.gnu.org/software/make/manual/html_node/
  [GnuWin32]: http://gnuwin32.sourceforge.net/packages/make.htm
  [Gow]: https://github.com/bmatzelle/gow

