# pyndex: the python reconstructor for the FTSE Russell US indexes.

## What is it?
pyndex is a Python package developed to reconstruct the Russell US indexes. It is based on the results of the following paper:

## Main features
Here are the main features of pyndex:

## Where to get it
The source code is currently hosted in the following GitHub repository folder: 

Binary installers for the latest released version are available at the Python package index and on conda.

```bash
# conda
conda install pyndex
```

```bash
# or PyPI
pip install pyndex
```

## License
The software is distributed under GNU General Public License v3.0.

## Usage

The package can reconstruct the Russell 1000, 2000 and 3000 index. The index to be reconstructed is passed via the argument **index** as **"1000"**, **"2000"** and **"3000"**, respectively. The oldest year supported is 1989. 

First, start your connection to the WRDS database. 
```python
>>> import wrds
>>> db = wrds.Connection()
Loading library list...
Done
```
Then pass your WRDS connection to the package along with the parameters year and index.
```python
>>> import pyndex as px
>>> index = px.Index.from_wrds(db, year = 2010, index = "3000")
>>> calendar = px.Index.get_calendar(year = 2010)
```
The method **px.Index.from_wrds()** will return a pandas MultiIndex DataFrame containing the index weights identified by **permno**, **permco** and **cusip**.
The method **px.Index.get_calendar()** will return the index reconstruction calendar for the corresponding year.

One can join a sequence of year in a single DataFrame using **px.join**.

```python
>>> index_2010 = px.Index.from_wrds(db, year = 2010, index = "3000")
>>> index_2011 = px.Index.from_wrds(db, year = 2011, index = "3000")
>>> new_index = px.join([index_2010,index_2011])
```

To check the difference of index constituents between two points in time you can use **px.diff** as follows,

```python
>>> index_2010 = px.Index.from_wrds(db, year = 2010, index = "3000")
>>> slice_1 = index_2010["2010-08-20","2010-08-20"]
>>> slice_2 = index_2010["2010-09-20","2010-09-20"]
>>> additions, deletions = px.diff(slice_1, slice_2)
```

The first value contains the index additions from slice_1 to slice_2 while the second one contains the index deletions.
This is particularly useful if one has to find the index addition between two index events, e.g. the annual rebalance and the Q3 quarterly additions.

In this case one would use px.diff as follows.

```python 
>>> index_2010 = px.Index.from_wrds(db, year = 2010, index = "3000")
>>> calendar_2010 = px.Index.get_calendar(2010)
>>> annual_rebalance = index_2010[calendar_2010["Annual Rebalance Day"]:calendar_2010["Annual Rebalance Day"]]
>>> q3_rebalance = index_2010[calendar_2010["Q3 Rebalance Day"]:calendar_2010["Q3 Rebalance Day"]]
>>> additions, deletions = px.diff(annual_rebalance, q3_rebalance)
```

## Getting Help
For any usage or installation questions, please get in touch with Alessandro Micheli at
am1118@ic.ac.uk .



