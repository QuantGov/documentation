---
title: Machine Learning
---

# Machine Learning with QuantGov

QuantGov provides a framework for training machine learning estimators using a
corpus, packaging those estimators, and using those models to make predictions
about other corpora.

QuantGov builds on the [scikit-learn](http://scikit-learn.org/stable/) library,
and users training new models should familiarize themselves with the models
they employ.

## Training a QuantGov Estimator

### Initializing the Estimator

To start a new quantgov estimator, use the following command
`quantgov start estimator myname` where `myname` is the name you want to give
to the estimator. This will copy the [estimator
skeleton](https://github.com/quantgov/estimator) to the folder with that name.

### Configuring the Training Framework

The estimator skeleton contains a `config.yaml` file that is used to configure
the training framework. It contains the following options:

-   **folds**: The number of folds to use for cross-validation
-   **scoring**: the metric for evaluating candidate models; see
    [here](http://scikit-learn.org/stable/modules/model_evaluation.html#common-cases-predefined-values)
    for available options.
-   **trainer\_corpus**: the path to the corpus used to train the estimator.

### Customize the Vectorizer

QuantGov estimators currently expect a `joblib`-pickled `scikit-learn`
vectorizer, such as a `CountVectorizer`. Most users will simply want to
customize the `CountVectorizer` defined in the `scripts/create_vectorizer.py`
script in the estimator skeleton.

Advanced users may implement their own vectorizers subclassing the
`sklearn.base.BaseEstimator` and the `sklearn.base.TransformerMixin` classes;
all vectorizers should take an iterable of text objects to their `fit` and
`fit_transform` methods.

In evaluating models, the vectorization step takes place before segmentation
for cross-valdiation; this means that users should take care not to let
information correlated with the variable of interest be used here. Instead,
include a transformer step in the candidate model pipeline, which is used after
the test-train split.

###
