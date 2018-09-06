# Bringing it All Together to Create a Dataset

A [QuantGov Project](../project.markdown) allows us to bring corpora and
estimators together to create a final dataset. In this tutorial, we are going
to use the estimator we trained previously to predict whether a document was
more likely to have been published in the "World" section of the 2015 *Federal
Register* than any other section. With that estimator, we'll predict whether
the documents from the 2016 *Federal Register* were published in the "World"
section or not.

## Initializing the Project

To start the project file, navigate to the same folder where you started the
2015 and 2016 corpora and the section estimator, and run the following command:

``` {.bash}
quantgov start project project-2016-sections
```

That command will create a new QuantGov estimator in the
`project-2016-sections` folder, with the following structure:

    estimator-fr-sections/
        .gitignore
        config.yaml
        README.md
        Snakefile
        data/
            .gitignore

Once again, the .gitignore files will be hidden on Mac and Linux.

The default QuantGov Project expects one estimator and one corpus for making
estimations, called the *target corpus*. This is the case for our tutorial and
for many use cases, but can also be expanded for more complex analyses.

## Configuring the Project

To configure the project, open the `config.yaml` file. By default, it looks
like this:

``` {.yaml}
name: quantgov-dataset
estimator: path/to/estimator
target_corpus: path/to/target/corpus
prediction_type: relevance
probability: False
```

For simple cases (including ours), this will be the only file you need to
modify. The `name` parameter gives the name that will be given to the output
folder and archives for the final dataset. The `estimator` and `target_corpus`
parameters are the paths to the relevant QuantGov components. The
`prediction_type` parameter gives the applicable name to the prediction output
file, and the `probability` parameter toggles binary and probability
classification output for relevant estimators.

Assuming you have started your corpora, estimator, and project from the same
folder, you should edit the file to look like this:

``` {.yaml}
name: 2016-sections
estimator: ../estimator-fr-sections
target_corpus: ../corpus-fr-2016
prediction_type: relevance
probability: False
```

Only the first three lines are changed. If your estimator and target corpus are
in different folders, simply specify the full paths as the values for those
parameters. Save the `config.yaml` folder.

## Creating the Binary Classification Dataset

To see the rules available in the project, navigate to the project's directory
in the command line and list them with snakemake:

``` {.bash}
snakemake -l
```

After all the foregoing, it may be anticlimactic to find that the command to
bring the corpora and estimator we've built in a combined dataset is simply:

``` {.bash}
snakemake create_dataset
```

After running that command there will be a folder with the name `2016-sections`
in the Project's `data` folder, so that the project structure looks like this:

    estimator-fr-sections/
        .gitignore
        config.yaml
        Snakefile
        data/
            .gitignore
            results.csv
            2016-sections/
                fr-2016_metadata.csv
                fr-2016_fr-section_relevance.csv

The `fr-2016_metadata.csv` file is the metadata from the `fr-2016` corpus. The
`fr-2016_fr-section_relevance.csv` file contains the actual prediction results.
Both use the same index for rows, so they can be easily merged for analysis in
any statistics program. But for now, open just the relevance file in your
spreadsheet editor.

The relevance file has three columns: two for the index of the documents in the
target corpus, and one for the `is_world` estimation result. The values are
binary: either `TRUE` or `FALSE`. Since the actual section is part of the
index, we can take a look and see how well our estimator performed.

In fact, it performs surprisingly well, getting more than 9 out of ten correct.
When we ran this model, we found that it correctly classified 93 percent of
documents. 70 percent of documents from the world section were labeled `TRUE`
(this measurement is called *recall*), and 97 percent of documents that were
not from the world section were labeled `False` (this measure is called
*specificity*).

## Archiving the Dataset

At this point, congratulations are in order. A finished dataset has been
created and is ready for analysis. This achievement should be saved for
posterity! Or at least, archived so that you can easily distribute it and can
make changes to the other parts of the QuantGov pipeline without fear of losing
the work we have so far.

On the command line, running the `create archive` rule will take the files from
the dataset folder and compress them in a zip archive. The archive's name will
be the name of the dataset with a date and time stamp; this allows the user to
create multiple versions of the same dataset and keep them straight. Create the
archive with the following command in the command prompt:

``` {.bash}
snakemake create_archive
```

## Switching From Binary to Probability estimates

Now that we've got our first dataset saved, we can make a second that contains
probability estimates instead of binary classifications. This functionality
will work with any scikit-learn estimator that provides the `predict_prob`
method, which is almost all of them (the major exceptions are Support Vector
Machines).

First, we need to delete the `2016-sections` folder and `results.csv` file from
the Project's `data` folder. Since the probability setting is in the
configuration file, Snakemake won't know that it needs to update these files,
so they must be manually removed.

Now, open the project's `config.yaml` file and change the value of the
`probability` parameter from `False` to `True`.

``` {.yaml}
name: 2016-sections
estimator: ../estimator-fr-section
target_corpus: ../corpus-fr-2016
prediction_type: relevance
probability: True
```

After saving that file, you can run the `create_archive` again, which will both
create the dataset and save it:

``` {.bash}
snakemake create_archive
```

Open the newly-created `fr-2016_fr-section_relevance.csv` file in the dataset
folder within the Project's `data` folder. As before, it contains three
columns, but now the `is_world` column contains probability estimates instead
of `True` and `False` values.

Sorting by the probability shows that those documents about which the estimator
is particularly certain are generally correct, while the incorrectly classified
documents hover near the 50 percent decision threshold.
