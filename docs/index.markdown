---
title: Home
---

# QuantGov

*A Policy Analytics Platform*

## About QuantGov

QuantGov is an effort to expand the frontiers of economic and legal
research by providing an open-source framework to uncover the latent
data in legal text. QuantGov grew out of the [RegData
project](https://quantgov.org/regdata), which seeks to solve the
longstanding problem of the quantification of federal regulation for use
in economic analysis.

The progression from RegData to QuantGov reflects two realizations:
first, that the kinds of tools used in RegData can be useful in other
contexts, and second, that a clear framework could ease adoption of
these methods and allow researchers to build off of one another's work
without constant reinvention of the wheel.

This documentation describes the QuantGov framework, which consists of
three parts:

- The [corpus](corpus.markdown), which defines a collection
of documents,
- A set of [natural language](nlp.markdown) analysis
functions that can be applied to the documents in a corpus, and
- A
system for training, packaging, and using [machine
learning](ml.markdown) estimators to make predictions about the
documents in a corpus.

QuantGov

## Get the Code

All code for official QuantGov projects is hosted on the [QuantGov
GitHub page](https://github.com/QuantGov). QuantGov currently maintains
four repositories:

-   [QuantGov/quantgov](https://github.com/QuantGov/quantgov), the
    QuantGov Library
-   [QuantGov/corpus](https://github.com/QuantGov/corpus), which holds
    the default QuantGov corpus skeleton.
-   [QuantGov/estimator](https://github.com/QuantGov/estimator), which
    holds the default QuantGov estimator skeleton.
-   [QuantGov/documentation](https://github.com/QuantGov/documentation),
    which holds this documentation.

## Installing QuantGov

QuantGov is available on the [Python Package
Index](https://pypi.org/project/quantgov/). It can be installed with the
following command:

``` {.bash}
pip install quantgov
```

QuantGov is compatible with Python versions 3.3 and up, and should be
installable on all operating systems.
