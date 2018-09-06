---
title: Estimator
---

# The QuantGov Estimator

## Basic Structure

An estimator is the file structure and scripts that retain and manage a
classification, regression, or unsupervised learning task. The root directory
of an estimator should contain the following:

-   A subdirectory named `data` containing the results from the evaluation and
    training of the estimator, alongside any intermediate data.
-   A subdirectory named `scripts` containing the scripts needed to evaluate
    candidate models and train the selected candidate using a trainer corpus
    and analyze a target corpus using the trained model.
-   A `snakefile` to manage the workflow of the estimator. The snakefile should
    implement the estimator interface (see below).

## The Snakefile Interface

The `snakefile` for an estimator should use the following variables, defined in
the `config.yaml` file (located in the root directory alongside `snakefile`):

-   `trainer_corpus` should be a path to the corpus needed to evaluate and
    train the estimator.
-   `folds` should be the number of times to fold the training data for
    testing.
-   `scoring` should be the method for testing the trained estimator.

## Writing a new Estimator

The easiest way to write a new estimator is to fork the most similar official
estimator (see below) and modify it for the relevant task. Generally, the tasks
for writing an estimator are the following:

-   Extract features from the training document.
-   Test one or more algorithms for the estimation task, possibly tuning a set
    of parameters for each algorithm.
-   Select an algorithm from the candidates tested and training it using the
    full trainer corpus.
-   Constructing a pipeline for feature extraction and analysis of other
    corpora, using the [corpus driver
    interface](corpus.markdown#the-corpus-driver-interface).

## Official QuantGov Estimators

Official QuantGov estimators are branches of the estimator repository,
available on GitHub at <https://github.com/QuantGov/estimator>. The current
official estimators are listed on the QuantGov Platform page at
<http://www.quantgov.org/platform>.

## Submitting a New Official Estimator

Complete estimators may be considered to be added as official QuantGov
estimator. If accepted, a new branch will be created to which a pull request
can be made. Additions to the official estimators are at the sole discretion of
the QuantGov team. Please email info\@quantgov.org with any questions about
adding an estimator to the official QuantGov estimator.
