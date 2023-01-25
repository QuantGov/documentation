# Building Your First Dataset

With a fully-built `QuantGov` corpus and an estimator, it's now possible
to build a full set of analyses into a dataset. Generally that will mean
running a number of natural language analyses, as well as a number of machine
learning analyses using the estimator.

## Running NLP Analyses

Let's start by running two built-in QuantGov analyses: the standard word
count, and the conditional count, which gives one measure of the
complexity of the document. Run these two commands from the corpus
root directory:

``` {.bash}
quantgov nlp count_words . -o wordcount.csv
quantgov nlp count_conditionals . -o conditionals.csv
```

## Running Machine Learning Analyses

Now we are ready to add in the machine learning estimates from the
estimator we trained earlier. Run the following command from the
estimator root directory:

``` {.bash}
quantgov ml estimate data/is_world_classifier.qge ../corpus-fr-2016 -o is_world.csv
```

Open the resulting `is_world.csv` in a spreadsheet editor or statistical
package, and you will see the familiar QuantGov analysis format: one
column for each index level, and another for the analysis value itself.
In this case, the results are True and False values because we trained a
binary classification model.

In this particular case, we can also see how well our classifier did
because the true value is right there in the first level of the index.
In my results, the classifier performed decently well out-of-the-box on
standard metrics: 93% accuracy, with a precision of .84, a recall of .7,
and an F1 score of .77; in practice, however, it would generally be
appropriate to further customize the estimator before training to
improve those scores even more.

QuantGov can also produce probability estimates instead of
classifications, by adding the `--probability` flag. Run the following
command from the estimator root directory:

``` {.bash}
quantgov ml estimate data/is_world_classifier.qge ../corpus-fr-2016 --probability -o is_world_prob.csv
```

Open the resulting `is_world_prob.csv` and you will see that instead of
True and False values, the estimates are probabilities of belonging to
the "world" section of the Federal Register. Sorting these results by
the probability shows that the documents with a very high probability
tend to actually be "world" section documents, while those with a very
low probability generally are not---exactly what we would want.

## Combining into a single dataset

We now have four pieces of data: word count, conditional count, is-world
classification, and is-world probability. They could certainly be
circulated separately, but QuantGov analyses are also designed to be
easily merged together using the index.

For example, create a file called `combine_datasets.py` in the folder
with your analysis results with these contents:

``` {.python}
import argparse
import pandas as pd

from pathlib import Path


def parse_args():
    parser = argparse.ArgumentParser()
    parser.add_argument('dataset', nargs='+', type=pd.read_csv)
    parser.add_argument('-o', '--outfile', required=True)
    return parser.parse_args()


def main():
    args = parse_args()
    results = args.dataset[0]
    for df in args.dataset[1:]:
        results = results.merge(df)
    results.to_csv(args.outfile, index=False)


if __name__ == "__main__":
    main()
```

Now in that folder, run the command:

``` {.bash}
python combine_datasets.py -o fr2016_isworld.csv wordcount.csv conditionals.csv is_world.csv is_world_prob.csv
```

Note: These files must be contained in a common directory for this
script to work properly.

The resulting `fr2016_isworld.csv` file will have all the results
generated, ready for further analysis and distribution. Similar scripts
or boilerplate are simple to produce for most statistical analysis
software.
