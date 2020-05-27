# JSONiq for JSON users
This tutorial introduces the JSONiq language, which declaratively manipulates JSON data.

# Static version

A user-friendly display of the tutorial is located [here](https://nbviewer.jupyter.org/github/ghislainfourny/jsoniq-tutorial/blob/master/JSONiq-tutorial.ipynb), if you just want to take a look with no setup.

# Interactive version

You can also download the tutorial from [here](https://github.com/ghislainfourny/jsoniq-tutorial/blob/master/JSONiq-tutorial.ipynb) and set it up on your machine with just a few easy steps, so that you can execute the queries yourself and even change them or enter your own.

If you choose to download and run this notebook (and have all the necessary software installed), we recommend installing [Anaconda](https://www.anaconda.com) to take care of this automatically. It is a popular data science software package with all you need.

## Zorba

The queries in the notebook also can be run on a local installation of [Zorba](http://zorba.io).

Some people like to use the [Zorba sandbox](http://try.zorba.io). If you do so, here are a few important comments:

- You will need to add

      jsoniq version "1.0";
    
before each query to tell Zorba that it's a JSONiq query. For a local installation, Zorba will recognize .jq files as JSONiq.

- This sandbox currently runs on an older version of Zorba (2.9), in which not all syntactic features were mature yet. Expect a few discrepancies, but look at the examples on the sandbox page for guidance on them. If you need the latest version, we suggest downloading Zorba 3.0 locally from [here](https://github.com/zorba-processor/zorba/releases/tag/4.0).
