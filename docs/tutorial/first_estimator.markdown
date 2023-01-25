# Training Your First Estimator

A [QuantGov Estimator](../ml.markdown) provides a machine learning model
for making predictions about text. Before an estimator can be used, it
needs to be told what it should be making predictions about and it needs
to be trained using the documents in a QuantGov corpus, called the
*trainer corpus*.

Our first estimator will predict whether or not a document is likely to
have been published in the "World" section of the *Federal Register*, as
opposed to a different section. We'll train this model using documents
from the 2015 *Federal Register* by following these steps:

1.  Build the trainer corpus
2.  Specify the labels
3.  Customize the Vectorizer
4.  Select candidate models
5.  Evaluate the models
6.  Train the best-performing model

## Building the Trainer Corpus

The trainer 2015 corpus will follow exactly the same structure as the
2016 corpus. Create a new corpus in the same folder in which you created
the 2016 corpus with the command:

``` {.bash}
quantgov start corpus corpus-fr-2015
```

Download the document archive
[here](https://s3.amazonaws.com/quantgov-datasets/workshop/2015_frdocs.zip).
As you did with the 2016 corpus, create a new `clean` folder in the
corpus's `data` folder, and extract the folders from the archive there
so that the section folders are directly within the `clean` folder. Then
modify the corpus's driver.py to look like this:

``` {.python}
import quantgov

from pathlib import Path

driver = quantgov.corpus.RecursiveDirectoryCorpusDriver(
    directory=Path(__file__).parent.joinpath('data', 'clean'),
    index_labels=('section', 'docno')
)
```

Save that file, and the corpus is ready to use in training. Note: In
this context we're using this corpus to train a model, but it's a
fully-fledged QuantGov corpus, and can be analyzed in its own right or
integrated into other QuantGov projects.

## Defining the Estimator

### Initialization

The estimator is its own separate component. In the same folder where
you created the two corpora, start an estimator with the command:

``` {.bash}
quantgov start estimator estimator-fr-sections
```

The estimator has the following structure:

    estimator-fr-sections/
        .gitignore
        README.md
        requirements.txt
        scripts/
            vectorize_trainers.py
            create_labels.py
            candidate_models.py
        data/
            .gitignore

Once again, the .gitignore files will be hidden on Mac and Linux.

### Vectorization

Before text can be analyzed it has to be transformed into arrays of
numbers, or *vectorized*. The default Vectorizer is laid out in
`scripts/vectorize_trainers.py`, and is an unmodified [scikit-learn
`CountVectorizer`](http://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.CountVectorizer.html).
A `CountVectorizer` does exactly what its name implies: it counts
individual words, mapping each word it encounters to a specific position
in the document vector. Each individual document is mapped to a vector
with a count of how many times each word in the entire directory appears
in that document.

Many modifications can be made to customize vectorization. The
`CountVectorizer` has arguments to specify word pattern, allow for
counting multi-word phrases, eliminate stop words, and more. There are
also other, more complex methods of vectorization, such as Google's
`word2vec` algorithm. The default setup, however, is a sufficient
starting place for this tutorial.

The `scripts/create_vectorizer.py` script will both vectorize the
trainers in the trainer corpus and save the vectorizer so that other
documents can later be vectorized in the same way. It takes one
positional argument, the path to the target corpus, and two keyword
arguments:

-   **-o**: The path to which to save the `quantgov.ml.Trainer` object,
    which holds the vectorized trainers
-   **--vectorizer\_output**: The path to which to save the Vectorizer
    object

Navigate into the estimator folder with the command
`cd estimator-fr-sections`, and run the following to create the trainers
and vectorizer:

``` {.bash}
python scripts/vectorize_trainers.py -o data/trainers --vectorizer_outfile data/vectorizer ../corpus-fr-2015
```

### Label Creation

To train a supervised model, we need to create a set of labels that tell
the model what we're looking for. In our case, we want to train the
model to identify whether or not a document is more likely to have been
published in the "World" section of the *Federal Register* than in any
other section. Since there are only two possible outcomes, our problem
is a case of *binary classification* and the labels we need to create
should have `True` or `False` values.

Label creation is handled in the `create_labels.py` script. By default,
this script randomly generates `True` and `False` labels, which is, by
design, not particularly useful. The important part of this script is
the `create_label` function, which begins on line 14 and looks like
this:

``` {.python}
def create_label(streamer):
    """
    Assign a label to a set of documents using a CountVectorizer
    Arguments:
    * streamer: a quantgov.corpus.CorpusStreamer object
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

This function takes a `CorpusStreamer` object, which iterates over a
corpus and saves the index. It returns a `Labels` object, which is made
up of three parts: the index to the labels, a sequence of names for each
kind of label generated, and the labels themselves.

Since we are only producing one kind of label---the binary label for
whether or not a document is part of the "World" section---we only need
one label name, which we can set as `is_world`.

Next, we need to change the line that defines the labels to generate
`True` values if the training document was published in the "World"
section, and `False` values otherwise. There are many ways to generate
your labels. You might want to look for the occurrence of a specific set
of words in the text, for example, or examine part of the metadata
generated by the corpus.

In our case, the section in which each document is published is part of
the index of the corpus trainer. Looking back at `driver.py`, we
remember that it is the first element, which is element number 0 in most
programming contexts. So by checking if the first element of the index
for each document is "world", we can generate our tables. Do that by
modifying the `create_label` function to look like this:

``` {.python}
def create_label(streamer):
    """
    Assign a label to a set of documents using a CountVectorizer
    Arguments:
    * streamer: a quantgov.corpus.CorpusStreamer object
    Returns: a quantgov.ml.Labels object
    """
    label_names = ('is_world',)
    labels = tuple(doc.index[0] == 'world' for doc in streamer)

    return quantgov.ml.Labels(
        index=tuple(streamer.index),
        label_names=label_names,
        labels=labels
)
```

Overall, the only changes to the `create_labels.py` are on lines 24 and
25. The conditional statement `doc.index[0] == 'world'` checks the first
element of the documents' `index` attribute, and evaluates to `True` if
that element is equal to the string "world". Otherwise, it evaluates to
`False`. This gives us the labels we need to train our algorithm. Save
the file.

The `create_labels.py` script takes one positional argument, the path to
the trainer corpus, and the `-o` keyword argument to specify the output
path for the labels object. In the estimator's root folder, run this
command to create the labels:

``` {.bash}
python scripts/create_labels.py -o data/labels ../corpus-fr-2015
```

### Candidate Model Specification

Our next step before we can evaluate our estimator is to specify a set
of candidate models with ranges of parameters.

We do this in the `candidate_models.py` script, by defining a sequence
of `CandidateModel` objects that specify each model (often several steps
collected together in a scikit-learn
[`Pipeline`](http://scikit-learn.org/stable/modules/generated/sklearn.pipeline.Pipeline.html))
and a dictionary of parameters to test, where the keys are the
parameter, and the dictionary values are sequences of the parameter
values to test.

To ease initial forays into using these models, the QuantGov library
includes starter sets for different types of problems. The default
QuantGov estimator uses the candidate set for a binary or multiclass
classification, which is proper for the problem being solved in this
tutorial. This candidate set consists of two pipelines. The first uses a
Term-Frequency-Inverse Document Frequency (TF-IDF) algorithm to
normalize word counts and a Logistic Regression Classifier with a ridge
penalty for classification. The second uses a TF-IDF for normalization
and a Random Forest Classifier for classification. The Logistic
Regression, or Logit, model is tested with different penalty
coefficients from -0.01 to 100. The Random Forests model is tested with
different numbers of trees from 5 to 100. For more on specifying
candidate models, see the [Machine Learning](../ml.markdown)
documentation.

### Evaluating Candidate Models

In the estimator root directory, run the following command:

``` {.bash}
quantgov ml evaluate scripts/candidate_models.py data/trainers data/labels data/evaluation.csv data/model.cfg
```

This command will use an exhaustive search approach to model evaluation,
trying every candidate model with every combination of parameters. It
evaluates models using cross validation, holding out one fold (from the
folds specified in the configuration file) and training the model on the
rest, then scoring the results from predicting the held-out fold. This
process is repeated holding out each fold once, producing a mean and
standard deviation which describe the probability distribution of the
underlying metric.

When the command finishes running, there will be two new files in the
`data` subdirectory of the estimator directory: `model_evaluation.csv`
and `model.cfg`. Open the `model_evaluation.csv` file in your favorite
spreadsheet editor. Each row of the CSV represents a single candidate
model and set of parameters. The most directly relevant columns are
`mean_train_score` and `std_train_score`, which contain the mean and
standard deviation for the evaluation metric---in our case, the F1
score---for that model with that set of parameters. A good rule of thumb
is to use the simplest model that's within one standard deviation of the
best-performing version.

In our case, the best-performing model is generally the Logit model with
a regularization coefficient of 100. If we open the `model.cfg` file in
a text editor, we see that this is the model that has been pre-loaded.

## Training the Selected Model

All that remains is to actually train the model that we have configured
in the `model.cfg` file using all the trainer documents. This can be
accomplished using the following command in the estimator folder:

``` {.bash}
quantgov ml train scripts/candidate_models.py data/model.cfg data/vectorizer data/trainers data/labels -o data/is_world_classifier.qge
```

The resulting model is packaged in the `is_world_classifier.qge` file in
the `data` folder. This model is ready to be used to build a QuantGov
dataset!
