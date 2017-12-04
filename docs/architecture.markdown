# The QuantGov Architecture

## Goals

The architecture used to create QuantGov has been designed to be modular, flexible, and extensible. We want a system where it is not only possible, but straightforward to add in or swap out either collections of text or machine learning algorithms to create new and innovative projects. To encourage others to build on our system, we have decided to adopt an open-source strategy. In addition, our code is inspired by the [Unix philosophy](http://www.catb.org/esr/writings/taoup/html/ch01s06.html), which encourages the writing of small programs that work together.

## Four Components

There are four components in the QuantGov architecture:

-   A **[corpus](http://docs.quantgov.org/corpus)** is the file structure and scripts that retain and manage a collection of text documents. A corpus will clean and organize text, generate metadata that describes the text, and provides a driver that scripts can use to read through the text in an ordered way. Some corpora also feature a download script that fetches the documents automatically.
-   An **[estimator](http://docs.quantgov.org/estimator)** is the file structure and scripts that retain and manage a classification, regression, or unsupervised learning task. Estimators should evaluate and tune candidate algorithms, train the chosen candidate, and provide a script that can perform an estimation on a given corpus.
-   A **[project](http://docs.quantgov.org/project)** combines corpora and estimators together to create a dataset. Projects provide a single point from which to run a corpus and estimator, and may perform some finishing or clean-up work on the final product.
-   A **[databank](http://docs.quantgov.org/databank)** holds external resources not automatically obtained or created by a corpus, estimator, or project.

For example, the RegData 3.0 project uses two corpora (one representing the Code of Federal Regulations for analysis and one representing the Federal Register to train the classifiers) and five estimators (one for each level of NAICS classification).

## Snakemake for Workflow Management

Corpora, estimators, and projects all use the program [Snakemake](http://snakemake.readthedocs.io/en/stable/) to manage workflow. Snakemake allows us to write *rules* that define how to create *output files* from *input files*. In addition, Snakemake allows users to create clear separation of tasks without mentally keeping track of every moving part - making it perfect for managing QuantGov workflows.

Most importantly, Snakemake ties together the pieces of a project without having to hard-code information that connects the pieces. Multiple projects can use the same predictor or corpus, because the corpus and predictor do not need to know anything about each other for the final analysis to complete successfully.

The Snakemake files in the standard project are fully commented and can be taken as examples. The [Snakemake documentation](http://snakemake.readthedocs.io/en/stable/) also provides convenient tutorials. Snakemake can be installed using `pip` or `conda` commands, as explained [here](http://snakemake.readthedocs.io/en/stable/getting_started/installation.html).
