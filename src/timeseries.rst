Using R for Time Series Analysis 
================================

Time series analysis
--------------------

This booklet itells you how to use the R statistical software to carry out some simple analyses
that are common in analysing time series data. 

This booklet assumes that the reader has some basic knowledge of time series analysis, and
the principal focus of the booklet is not to explain time series analysis, but rather 
to explain how to carry out these analyses using R.

If you are new to time series analysis, and want to learn more about any of the concepts
presented here, I would highly recommend the Open University book 
"Time series" (product code M249/02), available from
from `the Open University Shop <http://www.ouw.co.uk/store/>`_.

In this booklet, I will be using time series data sets that have been kindly made
available by Rob Hyndman in his Time Series Data Library at
`http://robjhyndman.com/TSDL/ <http://robjhyndman.com/TSDL/>`_. 

There is a pdf version of this booklet available at
`https://github.com/avrilcoghlan/LittleBookofRTimeSeries/raw/master/_build/latex/TimeSeries.pdf <https://github.com/avrilcoghlan/LittleBookofRTimeSeries/raw/master/_build/latex/TimeSeries.pdf>`_.

Reading in Time Series Data into R
----------------------------------

The first thing that you will want to do to analyse your time series data will be to read
it into R, and to plot the time series. You can read data into R using the scan() function,
which assumes that your data for successive time points is in a simple text file with one column. 

For example, the file `http://robjhyndman.com/tsdldata/misc/kings.dat <http://robjhyndman.com/tsdldata/misc/kings.dat>`_ contains data on the age of death of successive kings of England, starting
with William the Conqueror (original source: Hipel and Mcleod, 1994). 

The data set looks like this:

.. highlight:: r

::

    Age of Death of Successive Kings of England
    #starting with William the Conqueror
    #Source: McNeill, "Interactive Data Analysis"
    60
    43
    67
    50
    56
    42
    50
    65
    68
    43
    65
    34
    ...


Only the first few lines of the file have been shown. The first three lines contain
some comment on the data, and we want to ignore this when we read the data into R.
We can use this by using the "skip" parameter of the scan() function, which specifies
how many lines at the top of the file to ignore. To read the file into R, ignoring the
first three lines, we type:

.. highlight:: r

::

    > kings <- scan("http://robjhyndman.com/tsdldata/misc/kings.dat",skip=3)
      Read 42 items
    > kings
      [1] 60 43 67 50 56 42 50 65 68 43 65 34 47 34 49 41 13 35 53 56 16 43 69 59 48
      [26] 59 86 55 68 51 33 49 67 77 81 67 71 81 68 70 77 56
      
In this case the age of death of 42 successive kings of England has been read into the
variable 'kings'.

Once you have read the time series data into R, the next step is to store the data in
a time series object in R, so that you can use R's many functions for analysing time series data.
To store the data in a time series object, we use the ts() function in R. For example,
to store the data in the variable 'kings' as a time series object in R, we type:

.. highlight:: r

::

    > kingstimeseries <- ts(kings)
    > kingstimeseries 
      Time Series:
      Start = 1 
      End = 42 
      Frequency = 1 
      [1] 60 43 67 50 56 42 50 65 68 43 65 34 47 34 49 41 13 35 53 56 16 43 69 59 48
      [26] 59 86 55 68 51 33 49 67 77 81 67 71 81 68 70 77 56

Sometimes the time series data set that you have may have been collected at regular intervals that
were less than one year, for example, monthly or quarterly. In this case, you can specify the number
of times that data was collected per year by using the 'frequency' parameter in the ts() function. 
For monthly time series data, you set frequency=12, while for quarterly time series data, you set 
frequency=4. 

You can also specify the first year that the data was collected, and the first interval
in that year by using the 'start' parameter in the ts() function. For example, if the first
data point corresponds to the second quarter of 1986, you would set start=c(1986,2). 

An example is a data set of the number of births per month in New York city, from
January 1946 to December 1959 (originally collected by Newton). This data is available
in the file `http://robjhyndman.com/tsdldata/data/nybirths.dat 
<http://robjhyndman.com/tsdldata/data/nybirths.dat>`_
We can read the data into R, and store it as a time series object, by typing:

.. highlight:: r

::

    > births <- scan("http://robjhyndman.com/tsdldata/data/nybirths.dat")
      Read 168 items
    > birthstimeseries <- ts(births, frequency=12, start=c(1946,1))
    > birthstimeseries
        Jan    Feb    Mar    Apr    May    Jun    Jul    Aug    Sep    Oct    Nov    Dec
      1946 26.663 23.598 26.931 24.740 25.806 24.364 24.477 23.901 23.175 23.227 21.672 21.870
      1947 21.439 21.089 23.709 21.669 21.752 20.761 23.479 23.824 23.105 23.110 21.759 22.073
      1948 21.937 20.035 23.590 21.672 22.222 22.123 23.950 23.504 22.238 23.142 21.059 21.573
      1949 21.548 20.000 22.424 20.615 21.761 22.874 24.104 23.748 23.262 22.907 21.519 22.025
      1950 22.604 20.894 24.677 23.673 25.320 23.583 24.671 24.454 24.122 24.252 22.084 22.991
      1951 23.287 23.049 25.076 24.037 24.430 24.667 26.451 25.618 25.014 25.110 22.964 23.981
      1952 23.798 22.270 24.775 22.646 23.988 24.737 26.276 25.816 25.210 25.199 23.162 24.707
      1953 24.364 22.644 25.565 24.062 25.431 24.635 27.009 26.606 26.268 26.462 25.246 25.180
      1954 24.657 23.304 26.982 26.199 27.210 26.122 26.706 26.878 26.152 26.379 24.712 25.688
      1955 24.990 24.239 26.721 23.475 24.767 26.219 28.361 28.599 27.914 27.784 25.693 26.881
      1956 26.217 24.218 27.914 26.975 28.527 27.139 28.982 28.169 28.056 29.136 26.291 26.987
      1957 26.589 24.848 27.543 26.896 28.878 27.390 28.065 28.141 29.048 28.484 26.634 27.735
      1958 27.132 24.924 28.963 26.589 27.931 28.009 29.229 28.759 28.405 27.945 25.912 26.619
      1959 26.076 25.286 27.660 25.951 26.398 25.565 28.865 30.000 29.261 29.012 26.992 27.897   

Similarly, the file `http://robjhyndman.com/tsdldata/epi/mumps.dat 
<http://robjhyndman.com/tsdldata/epi/mumps.dat>`_ contains time series data on
the monthly reported number of cases of mumps in New York city, from 1928-1972 (original
data from Hipel and Mcleod, 1994). The first line of the file contains a comment, so
we need to set skip=1 in the scan() function to ignore it. We can read the data into R by typing:

.. highlight:: r

::

    > mumps <- scan("http://robjhyndman.com/tsdldata/epi/mumps.dat",skip=1)
      Read 534 items
    > mumpstimeseries <- ts(mumps, frequency=12, start=c(1928,1))
    > mumpstimeseries
      Jan  Feb  Mar  Apr  May  Jun  Jul  Aug  Sep  Oct  Nov  Dec
      1928  124  132  193  144  195  105   40   20   42   58  170  197
      1929  329  417  968  989 1264 1074  352  118   69   91  182  233
      1930  406  550  853  835  787  518  132   66   57   77   83  138
      1931  116  177  288  299  315  329  143   50   59   74  110  180
      1932  361  556  687  821  799  901  478  212  153  207  386  576
      1933  807  923 1604 1475 1123  662  253  116   70  107  147  155
      1934  266  249  406  452  547  472  213  136  114  137  276  341
      1935  688  935 1850 1938 1806 1030  472  151  123  133  195  255
      1936  357  386  543  645  668  659  276  142   93   82  122  225
      1937  315  360  777 1070 1115 1200  550  279  154  170  290  488
      1938  764  990 1695 1738 1295 1026  345  178   85  115  122  179
      1939  266  326  454  510  555  531  358  177  131  189  262  398
      1940  662  733  941 1229 1485 1406  959  436  233  221  391  696
      1941  930  899 1261 1245  847  626  348  168  132  142  201  399
      1942  569  651 1024 1272 1258  960  445  201  138  117  162  272
      1943  395  552  926 1062 1070  846  355  172  109   94  184  245
      1944  324  433  752  845  859  671  275  134   75  104  161  237
      1945  330  467  707  667  711  793  429  211  140  207  251  289
      1946  433  468  799 1138 1048  710  438  178  114  118  175  206
      1947  412  477  641  814  846  883  591  208  177  221  311  680
      1948 1105 1490 1956 1713 1291 1053  366  153   95  117  130  183
      1949  312  417  596  554  510  527  290  162   96   97  191  353
      1950  604  767 1116 1103 1330 1342  670  319  167  176  261  363
      1951  616  634  808  902 1003  833  475  243  167  152  182  279
      1952  520  615  745  838  787  827  570  254  182  185  268  530
      1953  813  785 1266 1495 1659 1532 1071  458  265  230  299  634
      1954  802  945 1220 1088  792  707  420  199  134  123  176  284
      1955  371  491  851  923  882  945  561  324  205  225  470  657
      1956 1066 1371 1674 1844 1819 1469  769  393  176  240  299  375
      1957  584  490  702  769  765  601  389  217  165  120  137  204
      1958  288  348  515  774  751  711  555  280  182  160  255  565
      1959  753  890 1183 1117  983  988  573  278  219  171  270  415
      1960  510  587  721  754  699  667  355  230  145  129  235  263
      1961  361  350  551  488  631  717  452  293  165  180  230  276
      1962  435  487  563  608  803  615  554  297  166  213  303  517
      1963  703  735 1037 1078  954  729  552  321  166  177  216  310
      1964  342  268  304  280  286  280  213  143  101  117  132  277
      1965  280  424  668  635  701  926  706  493  309  259  324  541
      1966  569  672 1020  848  754  765  437  255  166  130  165  166
      1967  235  261  390  424  500  440  294  167  126  124  162  182
      1968  274  349  420  607  572  496  415  240  158  124  135  100
      1969  164  148  298  506  567  639  447  283  158  223  212  333
      1970  330  281  354  527  463  429  300  147  104   90   77  122
      1971  146  149  248  300  223  294  235  110   88   68   88  132
      1972  149  157  219  221  264  298

Plotting a Time Series in R
---------------------------

Once you have read a time series into R, the next step is usually to make a plot of the time series
data, which you can do with the plot.ts() function in R.

For example, to plot the time series of the age of death of 42 successive kings of England, we type:

.. highlight:: r

::

    > plot.ts(kingstimeseries)

|image1|

We can see from the time plot that this time series could probably be described using an additive
model, since the random fluctuations in the data are roughly constant in size over time.

Likewise, to plot the time series of the number of births per month in New York city, we type:

.. highlight:: r

::

    > plot.ts(birthstimeseries)

|image2|

We can see from this time series that there seems to be seasonal variation in the number of
births per month: there is a peak every summer, and a trough every winter. Again, it seems 
that this time series could probably be described using an additive model, as the seasonal
fluctuations are roughly constant in size over time and do not seem to depend on the level
of the time series, and the random fluctuations also seem to be roughly constant in size over time.

Similarly, to plot the time series of the monthly reported number of cases of mumps in New York city,
we type:

.. highlight:: r

::

    > plot.ts(mumpstimeseries)

|image4|

Links and Further Reading
-------------------------

Some links are included here for further reading.

For a more in-depth introduction to R, a good online tutorial is
available on the "Kickstarting R" website,
`cran.r-project.org/doc/contrib/Lemon-kickstart <http://cran.r-project.org/doc/contrib/Lemon-kickstart/>`_.

There is another nice (slightly more in-depth) tutorial to R
available on the "Introduction to R" website,
`cran.r-project.org/doc/manuals/R-intro.html <http://cran.r-project.org/doc/manuals/R-intro.html>`_.

To learn about time series analysis, I would highly recommend the book "Time 
series" (product code M249/02) by the Open University, available from `the Open University Shop
<http://www.ouw.co.uk/store/>`_.

Acknowledgements
----------------

Thank you to Noel O'Boyle for helping in using Sphinx, `http://sphinx.pocoo.org <http://sphinx.pocoo.org>`_, to create
this document, and github, `https://github.com/ <https://github.com/>`_, to store different versions of the document
as I was writing it, and readthedocs, `http://readthedocs.org/ <http://readthedocs.org/>`_, to build and distribute
this document.

Many of the examples in this booklet are inspired by examples in the excellent Open University book,
"Time series" (product code M249/02), available from `the Open University Shop <http://www.ouw.co.uk/store/>`_.

Contact
-------

I will be grateful if you will send me (`Avril Coghlan <http://www.ucc.ie/microbio/avrilcoghlan/>`_) corrections or suggestions for improvements to
my email address a.coghlan@ucc.ie 

License
-------

The content in this book is licensed under a `Creative Commons Attribution 3.0 License
<http://creativecommons.org/licenses/by/3.0/>`_.

.. |image1| image:: ../_static/image1.png
.. |image2| image:: ../_static/image2.png
.. |image4| image:: ../_static/image4.png

