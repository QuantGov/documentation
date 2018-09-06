# Analyzing the Corpus

## Analysis with the QuantGov Library

Now that you have a working QuantGov corpus, you can use the QuantGov system to
analyze it. While it is easy to write scripts to analyze a corpus using the
driver interface, we have integrated a number of the most common tasks into the
QuantGov library itself. To see a all available commands, run the following in
a command line:

``` {.bash}
quantgov corpus -h
```

To analyze the *2016 Federal Register* corpus, navigate in your command prompt
to the directory above the corpus (the same one where you ran the
`quantgov start` command). Now run the following:

``` {.bash}
quantgov corpus count_words corpus-fr-2016
```

You should see a whole lot of lines whiz through your terminal. That's because,
by default, the QuantGov library outputs to the terminal in case you want to
re-direct that output to another program. But instead, let's save it as a file
by appending the `-o` flag (which stands for output):

``` {.bash}
quantgov corpus count_words corpus-fr-2016 -o wordcount.csv
```

Now you should have in that folder a file named `wordcount.csv` which you can
open in your favorite spreadsheet program. Notice that the spreadsheet has
three columns named "section", "docno", and "wordcount." The first two
correspond to the two parts of the index we configured in the corpus driver,
the last is the number calculated from the text---in this case, a word count.
This is the format for all QuantGov analysis: the index with one column per
part, and any calculated values in their own columns

## Analysis with Snakemake

While running QuantGov library commands by hand can be useful for one-off
analysis, for larger or more complicated projects you it is easier to collect
those steps into a workflow. QuantGov is designed to work with a workflow
management system called
[Snakemake](http://snakemake.readthedocs.io/en/stable/). Snakemake allows you
to define rules in a Snakefile on how to create output files, and can track
dependencies so that when you change one file, every rule that requires that
file to produce its output gets re-run later.

The default Snakefile defines rules to create a wordcount and restriction
count, and a rule to combine both those files to create a `metadata.csv` file.
The default Snakefile also tells the system to update the timestamp on the
driver whenever the folder on which it depends (`data/clean`) changes. This is
appropriate for when the input folder is created once and never changes (as in
our case), or when the entire folder is deleted and recreated when changes
happen.

To see all the available Snakemake rules, use your command prompt and navigate
into the corpus folder, then run the following command:

``` {.bash}
snakemake -l
```

The rule we want to execute is `create_metadata`, which we run as follows:

``` {.bash}
snakemake create_metadata
```

If you look in the corpus data folder, you should now see a `wordcount.csv`,
`restricions.csv` and a `metadata.csv`. If you open the `metadata.csv` file in
a spreadsheet program, you will see that it has the two index columns, then
word and regulatory restriction counts, all combined into a single file.

The Metadata file is an important part of the QuantGov architecture, and is an
excellent place to include both information directly derived from the text (as
seen here) and any other information that describes the document, such as
authors.
