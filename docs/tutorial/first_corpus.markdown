# Building Your First Corpus

A QuantGov [corpus](../corpus.markdown) is used to define a set of documents so
that they can be analyzed. A corpus holds the text files themselves, assigns a
unique index value to each document, and provides a driver that will feed the
index values and text to other parts of the QuantGov system.

## Create the Corpus

With the QuantGov library installed, we can create our first corpus with the
following command:

``` {.bash}
quantgov start corpus corpus-fr2016
```

That command will create a starter corpus in a new folder called
`corpus-fr2016`. Inside that folder you will see a file called `Snakefile`, a
file called `driver.py`, and a folder named `data`. The file structure should
look like this:

    corpus-fr2016/
        .gitignore
        driver.py
        Snakefile
        data/
            .gitignore

The `.gitignore` file only ensures that the empty data directory is created and
can be safely ignored.

## Structuring the Documents

The documents we will be using are proposed rules published in the *Federal
Register* in 2016. You can download these documents
[here](https://s3.amazonaws.com/quantgov-datasets/workshop/2016_frdocs.zip). The
*Federal Register* is divided into six different sections by topic, and the
dataset contains the first 100 rules published in each section that were not
corrections or withdrawals. Inside the zip folder, the documents are organized
by folders corresponding to the sections. The file names are the order in which
the rules for that section were obtained, and the files contain the text of the
rule proposal. So for example, the file at `money/0032.txt` inside the zip file
is the 32nd proposed rule that appeared in the *Federal Register*'s section
called "Money" in 2016.

To turn this set of documents into a QuantGov corpus, we need to do two things:
add them to the file structure, and tell `driver.py` how to provide them. To do
the first step, create a new folder in the corpus's `data` folder named
`clean`. Then unzip the downloaded file and copy the section folders into the
`data/clean` folder. The file structure should now look like this:

    corpus-fr2016/
        driver.py
        Snakefile
        data/
            .gitignore
            clean/
                business-and-industry/
                    0000.txt
                    0001.txt
                    [many other files]
                environment/
                    0000.txt
                    0001.txt
                    [many other files]
                [four other folders]

## Deciding on an Index

The corpus index is used to identify each document uniquely withing the corpus.
It can be as simple as the name of the file that the text is stored in, but can
also provide useful information. For example, the index used for the *Code of
Federal Regulations* corpus used to create RegData has three parts: the year
the in which the edition of the CFR was published, the CFR title, and the
individual part number. It is generally best to choose an index that
corresponds to the natural structure of the documents you're using.

For our 2016 *Federal Register* corpus, the most natural index has two parts:
the section of the *Federal Register* in which the document was published, and
its number within that section.

## Editing the Driver

The corpus driver tells the rest of the QuantGov system how to understand the
corpus. The driver is what actually reads the documents, identifies their
index, and provides them to other programs for analysis.

The QuantGov library contains a number of pre-built drivers for most common
situations. For the 2016 *Federal Register* corpus, we can use the default,
which is the `RecursiveDirectoryDriver`. The `RecursiveDirectoryDriver` uses
the folder and file names to provide the levels of the index. In our case, the
first part of the index will be the folder names, which are the sections of the
*Federal Register* in which the documents were published. The second part of
the index will be the file name, which is the document number.

The unmodified driver in the corpus looks like this:

``` {.python}
import quantgov

from pathlib import Path

driver = quantgov.corpora.RecursiveDirectoryCorpusDriver(
    directory=Path(__file__).parent.joinpath('data', 'clean'),
    index_labels='filename'
)
```

This is already almost exactly what we need. The one change we need to make is
to the `index_label` argument being passed in: we need it to reflect the two
levels of our index---the *Federal Register* section and document number. Edit
the driver file so that it looks like this:

``` {.python}
import quantgov

from pathlib import Path

driver = quantgov.corpora.RecursiveDirectoryCorpusDriver(
    directory=Path(__file__).parent.joinpath('data', 'clean'),
    index_labels=('section', 'docno')
)
```

Save the file. Congratulations! The corpus is now ready for [analysis](documentation/docs/tutorial/corpus_analysis.markdown).

