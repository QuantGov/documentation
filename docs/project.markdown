# The QuantGov Project

## Basic Structure

A QuantGov project brings together corpora and projects to create a new dataset. The root directory for a project should contain:

-   A `makefile` to manage the workflow of the project.
-   A subdirectory named `scripts` containing any scripts needed to clean, combine, or polish metadata and analyses from the corpora and projects
-   A subdirectory named `data` that holds any intermediate data generated in the creation of the dataset
-   Optionally, a subdirectory named `release` that holds the finalized dataset or datasets and any other information as suitable for distribution.

## Writing a New Project

The easiest way to write a new project is to fork the most similar official project (see below) and modify it for the relevant task. The `makefile` for a project should make use of the [estimator interface](http://docs.quantgov.org/estimator/#the-estimator-interface), and of the corpus feature of providing a combined metadata file in the file `data/metadata.csv`. Projects should use the [recursive function of make](https://www.gnu.org/software/make/manual/html_node/Recursion.html#Recursion), to ensure that the desired files are created and up to date before putting them in their final formats.

## Official QuantGov projects

Official QuantGov projects are branches of the project repository, available on GitHub at <https://github.com/QuantGov/project>. The current official projects are listed on the QuantGov Platform page at <http://www.quantgov.org/platform>.

## Submitting a New Official Project

Complete projects may be considered to be added as official QuantGov project. If accepted, a new branch will be created to which a pull request can be made. Additions to the official projects are at the sole discretion of the QuantGov team.

