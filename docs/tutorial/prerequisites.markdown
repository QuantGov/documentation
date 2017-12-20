# Pre-Requisites

## The Command Line

While many computer programs today use a graphical user interface, using
QuantGov involves running programs using the command line. If you are not
familiar with the command line, you should work through
[this](https://tutorial.djangogirls.org/en/intro_to_command_line/) tutorial and
familiarize yourself with how it works before completing the rest of this
tutorial. In particular, before moving on you should feel comfortable with:

-   Navigating to different folders.
-   Moving, copying and deleting files.
-   Running commands with arguments.

## A Text Editor

The built-in editors for both Windows and Mac are not well-suited for working
with QuantGov (or anything else that requires meaningful work with code). If
you do not already have a more powerful editor that you like, good
cross-platform options include:

-   [SublimeText](https://www.sublimetext.com/): A sophisticated text editor
    for code, markup, and prose.
-   [Atom](https://atom.io/): A hackable text editor for the 21st Century.

## Installing Python

QuantGov makes extensive use of the Python programming language and Python
libraries (although it can be extended to use other languages as well). If you
do not have Python installed already, the easiest way is to use the Anaconda
Python distribution. Go to <https://conda.io/miniconda.html> and use the Python
3 (**not** Python 2) installer appropriate for your system (most users will
want the 64-bit version):

-   For Windows: Double-click the installer and follow the prompts
-   For Mac: Open a command line and navigate to the folder where you
    downloaded the installer. Run: `bash Miniconda3-latest-MacOSX-x86_64.sh`
-   For Linux: Open a command line and navigate to the folder where you
    downloaded the installer. Run: `bash Miniconda3-latest-Linux-x86_64.sh`

## Installing QuantGov

The QuantGov framework and all dependencies can be installed with `pip`, a
package manager that is included with recent versions of Python. To install
QuantGov, run the following command on the command line:

``` {.bash}
pip install -U quantgov
```

