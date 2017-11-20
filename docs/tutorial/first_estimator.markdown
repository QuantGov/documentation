# Training Your First Estimator

A [QuantGov Estimator](../estimator.markdown) provides a machine learning model
for making predictions about text. Before an estimator can be used, it needs to
be told what it should be making predictions about and it needs to be trained
using the documents in a QuantGov corpus, called the *trainer corpus*.

Our first estimator will predict whether or not a document is likely to have
been published in the "World" section of the *Federal Register*, as opposed to
a different section. We'll train this model using documents from the 2015
*Federal Register* by following steps:

1.  Build the trainer corpus
2.  Specify the labels
3.  Customize the Vectorizer
4.  Select candidate models
5.  Evaluate the models
6.  Train the best-performing model

## Building the Trainer Corpus

The trainer 2015 corpus will follow exactly the same structure as the 2016
corpus. Create a new corpus in the same folder in which you created the 2016
corpus with the command:

``` {.bash}
quantgov start corpus corpus-fr-2015
```

Download the document archive
[here](https://s3.amazonaws.com/quantgov-datasets/workshop/2015_frdocs.zip). As
you did with the 2016, corpus, create a new `clean` folder in the corpus's
`data` folder, and extract the folders from the archive there so that the
section folders are directly within the `clean` folder. Then modify the
corpus's driver.py to look like this:

``` {.python}
import quantgov

from pathlib import Path

driver = quantgov.corpora.RecursiveDirectoryCorpusDriver(
    directory=Path(__file__).parent.joinpath('data', 'clean'),
    index_labels=('section', 'docno')
)
```

Save that file, and the corpus is ready to use in training. NB: In this context
we're using this corpus to train a model, but it's a fully-fledged QuantGov
corpus, and can be analyzed in its own right or integrated into other QuantGov
projects.

## Defining the Estimator

### Initialization

The estimator is its own separate component. In the same folder where you
created the two corpora, start an estimator with the command:

``` {.bash}
quantgov create estimator estimator-fr-sections
```

The estimator has the following structure:

    estimator-fr-sections/
        .gitignore
        config.yaml
        Snakefile
        scripts/
            vectorize_trainers.py
            create_labels.py
            candidate_models.py
        data/
            .gitignore

### Vectorization

Before text can be analyzed it has to be transformed into arrays of numbers, or
*vectorized*. The default Vectorizer is laid out in `vectorize_trainers.py`,
and is an unmodified [scikit-learn
`CountVectorizer`](http://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.CountVectorizer.html)
A `CountVectorizer` does exactly what its name implies: it counts individual
words, mapping each word it encounters to a specific position in the document
vector. Each individual document is mapped to a vector with a count of how many
times each word in the entire directory appears in that document.

Many modifications can be made to customize vectorization. The
`CountVectorizer` has arguments to specify word pattern, allow for counting
multi-word phrases, eliminate stop words, and more. There are also other, more
complex methods of vectorization, such as Google's `word2vec` algorithm. The
default setup, however, is a sufficient starting place for this tutorial.

### Label Creation

