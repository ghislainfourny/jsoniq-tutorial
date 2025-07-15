# JSONiq for JSON users
This tutorial introduces the JSONiq language, which declaratively manipulates JSON data.

# Live version

It was moved to an interactive, live tutorial that can be found [here](https://colab.research.google.com/github/RumbleDB/rumble/blob/master/RumbleSandbox.ipynb).

# Static version

A user-friendly display of the tutorial is located [here](https://nbviewer.jupyter.org/github/RumbleDB/rumble/blob/master/RumbleSandbox.ipynb), if you just want to take a look with no setup: just ignore the instructions and scroll your way down.

# New: JSONiq for Python!

You can run JSONiq queries in Python with our [jsoniq package](https://pypi.org/project/jsoniq/) (Java 17 or 21 required with JAVA_HOME correctly set) with this simple install command:

```
pip install jsoniq
```

Hello world query:

```
from jsoniq import RumbleSession

rumble = RumbleSession.builder.getOrCreate();

print(rumble.jsoniq('{ "foo": [ 6*7 ] }').json());
```

The jsoniq package contains RumbleDB running on top of pyspark. It can be used on your laptop for small or medium amounts of data, or on large clusters on Petabytes of data.

# Running JSONiq on your own laptop or cluster

You can also install RumbleDB to run on the command line on your laptop or on a large cluster (Amazon EMR, Azure HDInsight, Google Cloud...). The documentation is [here](https://rumble.readthedocs.io/en/latest/Getting%20started/).

## Zorba

The queries in the notebook also can be run on a local installation of [Zorba](http://zorba.io). However, Zorba is no longer officially maintained.
