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

Once again, the .gitignore files will be hidden on Mac and Linux.

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

To train a supervised model, we need to create a set of labels that tell the
model what we're looking for. In our case, we want to train the model to
identify whether or not a document is more likely to have been published in the
"World" section of the *Federal Register* than another section. Since there are
only two possible outcomes, our problem is a case of *binary classification*
and the labels we need to create should have `True` or `False` values.

Label creation is handled in the `create_labels.py` script. By default, this
script randomly generates `True` and `False` labels, which is, by design, not
particularly useful. The important part of this script is the `create_label`
function, which begins on line 14 and looks like this:

``` {.python}
def create_label(streamer):
    """
    Assign a label to a set of documents using a CountVectorizer
    Arguments:
    * streamer: a quantgov.corpora.CorpusStreamer object
    Returns: a quantgov.estimator.Labels object
    """
    label_names = ('randomly_true',)
    labels = tuple(random.choice([True, False]) for doc in streamer)

    return quantgov.estimator.Labels(
        index=tuple(streamer.index),
        label_names=label_names,
        labels=labels
)
```

This function takes a `CorpusStreamer` object, which iterates over a corpus and
saves the index. It returns a `Labels` object, which is made up of three parts:
the index to the labels, a sequence of names for each kind of label generated,
and the labels themselves.

Since we are only producing one kind of label---the binary label for whether or
not a document is part of the "World" section---we only need one label name,
which we can set as `is_world`. Then we need to change the line that defines
the labels to generate `True` values if the training document was published in
the "World" section, and `False` values otherwise.

There are many ways to generate your labels. You might want to look for the
occurrence of a specific set of words in the text, for example, or examine part
of the metadata generated by the corpus. In our case, the section in which each
document is published is part of the index of the corpus trainer. Looking back
at `driver.py`, we remember that it is the first element, which is element
number 0 in most programming contexts. So by checking if the first element of
the index for each document is "world", we can generate our tables. Do that by
modifying the `create_label` function to look like this:

``` {.python}
def create_label(streamer):
    """
    Assign a label to a set of documents using a CountVectorizer
    Arguments:
    * streamer: a quantgov.corpora.CorpusStreamer object
    Returns: a quantgov.estimator.Labels object
    """
    label_names = ('is_world',)
    labels = tuple(doc.index[0] == 'world' for doc in streamer)

    return quantgov.estimator.Labels(
        index=tuple(streamer.index),
        label_names=label_names,
        labels=labels
)
```

The only changes are on lines 24 and 25. The conditional statement
`doc.index[0] == 'world'` checks the first element of the documents' `index`
attribute, and evaluates to `True` if that element is equal to the string
"world". Otherwise, it evaluates to `False`. This gives us the labels we need
to train our algorithm. Save the file.

### Candidate Model Specification

Our last step before we can evaluate our estimator is to specify a set of
candidate models with ranges of parameters.
