# Natural Language Analysis

The QuantGov library includes a number of built-in utilities to perform
some natural language analysis on a corpus. These can be run using the
`quantgov nlp` set of commands. All nlp analyses are run at the document
level, and output a csv. By default, results are printed to standard
output, but an output file may be specified with the `-o` flag.

For example, the command

``` {.bash}
quantgov nlp count_words corpora/mycorpus -o wordcount.csv
```

would produce a word count for the corpus contained in the folder
`corpora/mycorpus` and save it in the file `wordcount.csv`. The output
will include one column per index label, with the index label name as a
header, and then the results of the analyses. The results of `nlp`
analyses may therefore be merged with any data or statistical analysis
software to create a combined dataset.

Note that not all analyses may be aggregated above the document level;
users should familiarize themselves with the metrics produced and use
them appropriately.

## Available Analyses

-   **count\_words**: Count the number of words in a document. The
    regular expression defining a word is `\b\w+\b'` by default; this
    may be overridden with the `--word_pattern` argument.
-   **count\_occurrences**: Count the non-overlapping occurrences of a
    list of words, phrases, or regular expressions. The longest items in
    the list take precedence. A total column may be specified with the
    `--total_label` argument.
-   **shannon\_entropy**: Calculate Shannon entropy for the document.
    Requires the `textblob` library.
-   **conditional\_counter**: Count occurrences of the words and phrases
    "if", "but", "except", "provided", "when", "where", "whenever",
    "unless", "notwithstanding", "in the event", and "in no event".
-   **sentence\_length**: Calculate average sentence length. Requires
    the `textblob` library
-   **sentiment\_analysis**: Produce a quantification of polarity and
    subjectivity for a document. Requires `textblob` library.
