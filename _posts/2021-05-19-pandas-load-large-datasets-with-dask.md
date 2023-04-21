---
layout: post
title: "Pandas Load Large Datasets with Dask"
description: "Loading large datasets with Dask instead of Pandas."
author: tom
image: assets/images/1.jpg
tags: [analytics, python, pandas]
---

Loading large datasets with Dask instead of Pandas.

I tried loading an 8GB file with Pandas but it crashed. You can do this with Dask.

More about Dask [here](https://docs.dask.org/en/latest/index.html)

Install Dask with conda:

    $ conda install dask

Then setup:

    $ python
    > import dask.dataframe as dd

Read a CSV the same way as Pandas:

    > df = dd.read_csv("file.csv")

First time I tried, I got errors about data types `Mismatched dtypes found`. I doesn't infer the data types correctly.

Use `dtype` and pass a JSON with column names and values:

    > df = dd.read_csv("file.csv", dtype={'Column1': int, 'Column2': str})

You can do the same operations as Pandas:

    > df.head()
    > df.tail()
    > df.dtypes

Find other methods:

    > dir(df)

More:

* [Dask dataframes](https://tutorial.dask.org/04_dataframe.html)

[![Ask me anything on Linkedin]({{ site.baseurl }}/assets/images/ama-linkedin-tomordonez.png)](https://www.linkedin.com/in/tomordonez/)