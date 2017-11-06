# Databanks

Databanks hold resources that are used by one or more corpora, estimators, or projects. Databanks are kept separate from the corpus, estimator, or project using them so that the version control history does not become clogged with large files that generally do not benefit from version control.

Unlike the other pieces in the QuantGov architecture, databanks do not need to be arbitrarily modular, and therefore do not follow any interface. Often, a databank will simply be a directory with files in it. Not every corpus, estimator, or project will require a databank.

Some cases where a databank might be useful are:

-   A corpus where a raw set of documents cannot be obtained automatically, e.g., over the internet.
-   An estimator that specifies a specific external vocabulary to vectorize documents
-   A project that will include documentation or license information in the final release

From the perspective of the corpus, estimator, or project, the databank should be considered read-only; data that is generated or modified should be stored in the `data` folder, not the databank.

If a corpus, estimator, or project requires a databank, a README file should explain how to obtain it. The paths to databanks employed should be stored as variables in the relevant `snakefile` so that they only need to be changed once.

