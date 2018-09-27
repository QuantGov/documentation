---
title: Machine Learning
---

# Machine Learning with QuantGov

QuantGov provides a framework for training machine learning estimators
using a corpus, packaging those estimators, and using those models to
make predictions about other corpora.

QuantGov builds on the [scikit-learn](http://scikit-learn.org/stable/)
library, and users training new models should familiarize themselves
with the models they employ.

## Training a QuantGov Estimator

### Initializing the Estimator

To start a new quantgov estimator, use the following command
`quantgov start estimator myname` where `myname` is the name you want to
give to the estimator. This will copy the [estimator
skeleton](https://github.com/quantgov/estimator) to the folder with that
name.

### Customize the Vectorizer and Vectorize Trainers

In order to use mathematical statistical techniques on text documents,
those documents need to be converted to set of numbers, or vector. This
process is called *vectorization*, and the Python objects used to create
carry this out are called *vectorizers*.

QuantGov estimators currently expect a `joblib`-pickled `scikit-learn`
vectorizer, such as a `CountVectorizer`. Most users will simply want to
customize the `CountVectorizer` defined in the
`scripts/create_vectorizer.py` script in the estimator skeleton.

Advanced users may implement their own vectorizers subclassing the
`sklearn.base.BaseEstimator` and the `sklearn.base.TransformerMixin`
classes; all vectorizers should take an iterable of text objects to
their `fit` and `fit_transform` methods.

In evaluating models, the vectorization step takes place before
segmentation for cross-valdiation; this means that users should take
care not to let information correlated with the variable of interest be
used here. Instead, include a transformer step in the candidate model
pipeline, which is used after the test-train split.

For efficiency, the trainer corpus is expected to be vectorized before
model evaluation. This can be accomplished with the command
`quantgov ml vectorize` which takes two positional arguments:

-   **vectorizer**: the path to the saved vectorizer object
-   **corpus**: the path to the target corpus

The following argument must also be specified:

-   **--outfile** or **-o**: the path to which to save the vectorized
    Trainers.

### Generating the Labels

If you are training a supervised model---that is, a classification model
or regression model---you need to generate the training values for the
documents in the trainer corpus. An example generating (meaningless)
labels is in the skeleton estimator in the script
`scripts/generate_labels.py`. Labels should be stored in a
`quantgov.ml.Labels` object, which takes three arguments:

-   **index**: a sequence holding the index values for each document
    being labeled.
-   **label\_names**: a sequence holding one name for each label. NB:
    even when there is only one label, this parameter should be a
    sequence, such as `['label']` or `('label',)`.
-   **labels**: an array-like of label values with shape \[n\_samples x
    n\_labels\].

The `Labels` object has a `save` method which can be used to save the
object to a file.

For classification problems, the nature of the `Labels` object
determines the type of classification model to be trained. If the values
are `True` and `False`, the problem is assumed to be binary
classification. If other values are given, the problem is assumed to be
multiclass. If a numpy array or pandas DataFrame of zeroes and ones are
given, the problem is assumed to be multilabel classification, where
each row is assumed to represent a document and each column is assumed
to represent a label.

### Specifying candidate models

QuantGov is able to test multiple candidate models, each with a variety
of hyperparameters. Candidate Models must be specified in a Python
module as a module-level variable named `models` which is a sequence of
`quantgov.ml.CandidateModel` objects. CandidateModels are initialized
with three arguments:

-   **name**: the user-facing name of the model
-   **model**: a scikit-learn estimator or pipeline, or a class that
    implements the scikit-learn interface
-   **parameters**: a dictionary of hyperparameters to tune, where the
    keys are the parameter names and the values are a sequence of
    possible values. See the documentation for `grid_param` in the
    scikit-learn documentation in the [narrative
    documentaion](http://scikit-learn.org/stable/modules/grid_search.html#exhaustive-grid-search)
    and in the [api
    documentaion](http://scikit-learn.org/stable/modules/generated/sklearn.model_selection.GridSearchCV.html#sklearn.model_selection.GridSearchCV).

Starter sets of candidate models can be seen in the
`quantgov.ml.candidate_sets`
[module](https://github.com/QuantGov/quantgov/blob/qg2/quantgov/ml/candidate_sets.py).
These can be imported directly (as in the estimator skeleton) or copied
and customized as needed.

### Evaluating Candidate Models

At this point, things get easier. The candidate models can be trained
using the `quantgov ml evaluate` command, which takes the following
positional arguments:

-   **modeldefs**: the path to the module defining the candidate models
-   **trainers**: the path to the saved `Trainers` object
-   **labels**: the path to the saved `Labels` object
-   **output\_results**: the path to a csv file which will list the
    results of every model evaluated, with every combination of
    hyperparameters.
-   **output\_suggestion**: the path to a file which will hold the
    configuration of the best performing models.

The following arguments are optional:

-   **--folds** (defaults to 5): the number of folds to use in
    cross-validation.
-   **--score** (defaults to `'f1`'): the scoring metric for comparing
    models. See the [scikit-learn
    documentation](http://scikit-learn.org/stable/modules/model_evaluation.html#common-cases-predefined-values)
    for a list of valid options.

The evaluation command will automatically select the highest performing
score and output its model and parameters to the file specified in the
`output_suggestion` parameter. Users should inspect the full scoring
results, however, and select a model that balances simplicity and
performance. One common choice, for example, is to employ the simplest
set of values which performs within one standard deviation of the best
model. The desired configuration can be set by editing the suggestion
file, which follows the
[ConfigParser](https://docs.python.org/library/configparser.html)
format.

### Training the selected model.

Once a model has been selected and the configuration file edited, the
final model can be trained on the full trainer set with the
`quantgov ml train` command, which takes the following positional
arguments:

-   **modeldefs**: the path to the module defining the candidate models
-   **configfile**: the path to the configuration file defining the
    model
-   **trainers**: the path to the saved `Trainers` object
-   **labels**: the path to the saved `Labels` object

The following argument must also be specified:

-   **--outfile** or **-o**: the path to which to save the estimator. By
    convention, QuantGov estimators have the extension `.qge`.

The resulting Estimator is a self-contained file that can be
distributed; however, users may need to ensure that they are using
compatible versions of the underlying libraries, such as scikit-learn
and pandas; these should be documented by the author of the estimator.

## Using a QuantGov Estimator to Make Predictions

With a trained QuantGov estimator, predictions about a new corpus can be
made with the `quantgov ml estimate` command, which takes the following
positional arguments:

-   **estimator**: the path to the estimator file
-   **corpus**: the path to the corpus to be analyzed

The following optional arguments are also available:

-   **--probability**: for a classification problem, produce probability
    estimates instead of class predictions
-   **--precision** (defaults to 4): when probabilities are produced,
    the desired number of decimal points of precision
-   **--outfile** or **-o** (defaults to standard out): the path to a
    file where results should be saved.
