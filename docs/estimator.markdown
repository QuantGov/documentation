# The QuantGov Estimator

## Basic Structure

An estimator represents a machine learning algorithm, such as those for classification, regression, clustering, or topic modeling classes. The root directory of an estimator should contain the following:

-   A `makefile` to manage the workflow of the estimator. The makefile should implement the estimator interface (see below).
-   A subdirectory named `scripts` containing the scripts needed to evaluate candidate models and train the selected candidate using a trainer corpus and analyze a target corpus using the trained model
-   A subdirectory named `data` that holds any intermediate data generated in the evaluation and training of the estimator.

## The Estimator Interface

The `makefile` for an estimator should use the following variables. Default values can be set for these variables in the `makefile`, because they can be overridden in project `makefiles`.

-   `TRAINER_CORPUS` should be a path to the corpus needed to evaluate and train the estimator.
-   `TARGET_CORPUS` should be a path to the corpus to be analyzed by the trained estimator.
-   `RESULTS` should be the output path for the results of the analysis of the target corpus.

The `makefile` for an estimator should also provide two [phony targets]:

-   `evaluate` should run the scripts that evaluate candidate models and propose one for training with the full corpus.
-   `estimate` should analyze the target corpus with the trained model

Phony targets are used to keep estimators as language-agnostic as possible.

## Writing a new Estimator

The easiest way to write a new estimator is to fork the most similar official estimator (see below) and modify it for the relevant task. Generally, the tasks for writing an estimator are:

-   Extracting features from the training document
-   Testing one or more algorithms for the estimation task, possibly tuning a set of parameters for each algorithm
-   Selecting an algorithm from the candidates tested and training it using the full trainer corpus
-   Constructing a pipeline for feature extraction and analysis of other corpora, using the [corpus driver interface].

Estimators should always make a [recursive make call] on the drivers of the training and target corpora before using them to ensure that they are up to date.

## Official QuantGov Estimators

Official QuantGov estimators are branches of the estimator repository. The current official estimators are:

-   The generic estimator, which:
    -   Uses term counts to vectorize documents;
    -   Extracts a dummy binary of labels from the index of the documents;
    -   Tunes a Random Forests classifier with a TF-IDF preprocessor;
    -   Proposes a model in a user-editable file named `data/model.config`;
    -   Trains the model described in `data/model.config`; and
    -   Analyzes an arbitrary corpus using the trained model.

    For many tasks, all that is required to create a new estimator is to edit `scripts/models.py` to include the desired candidate algorithms and hyperparameters, and to set the correct scoring function in `scripts/evaluate.py`. See the `README` for the generic estimator for more details. Download the generic estimator [here] or fork it on GitHub [here][1].

-   The NMF estimator, a modification of the Generic estimator which trains a non-negative matrix factorization model for topic modeling. Download the NMF estimator [here][2] or fork it on GitHub [here][3].

## Submitting a New Official Estimator

Complete estimators may be considered to be added as official QuantGov estimator. If accepted, a new branch will be created to which a pull request can be made. Additions to the official estimators are at the sole discretion of the QuantGov team.

  [phony targets]: https://www.gnu.org/software/make/manual/make.html#Phony-Targets
  [corpus driver interface]: corpus.markdown#the-corpus-driver-interface
  [recursive make call]: https://www.gnu.org/software/make/manual/html_node/Recursion.html#Recursion
  [here]: https://github.com/QuantGov/estimator/archive/master.zip
  [1]: https://github.com/QuantGov/estimator
  [2]: https://github.com/QuantGov/estimator/archive/nmf.zip
  [3]: https://github.com/QuantGov/estimator/tree/nmf

