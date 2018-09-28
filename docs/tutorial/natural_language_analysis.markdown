# Natural Language Analysis

Now that you have a working QuantGov corpus, you can use the QuantGov
system to analyze it. While it is easy to write scripts to analyze a
corpus using the driver interface, we have integrated a number of the
most common tasks into the QuantGov library itself. To see a all
available commands, run the following in a command line:

``` {.bash}
quantgov corpus -h
```

You can also find a description of these commands in the [Natural
Language](../nlp.markdown) section of this documentation.

To analyze the *2016 Federal Register* corpus, navigate in your command
prompt to the directory above the corpus (the same one where you ran the
`quantgov start` command). Now run the following:

``` {.bash}
quantgov nlp count_words corpus-fr-2016
```

You should see a whole lot of lines whiz through your terminal. That's
because, by default, the QuantGov library outputs to the terminal in
case you want to re-direct that output to another program. But instead,
let's save it as a file by appending the `-o` flag (which stands for
output):

``` {.bash}
quantgov nlp count_words corpus-fr-2016 -o wordcount.csv
```

Now you should have in that folder a file named `wordcount.csv` which
you can open in your favorite spreadsheet program. Notice that the
spreadsheet has three columns named "section", "docno", and "words". The
first two correspond to the two parts of the index we configured in the
corpus driver, the last is the number calculated from the text---in this
case, a word count. This is the format for all QuantGov analysis: the
index with one column per part, and any calculated values in their own
columns
