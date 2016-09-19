## WikiRacer
WikiRacer is a web crawler that tries to find the shortest path between two Wikipedia articles.

***

### Requirements
The script was written in Python 2.7 and uses the `json`, `argparse`, `requests`, `BeautifulSoup`, `collections`, and `time` packages.

***

### Methods
WikiRacer takes a breadth-first search (BFS) approach to finding possible paths. WikiRacer explores paths by going down one level at a time: it first looks at all children links from the starting page, then looks at each child's links (and so forth). This approach requires a lot of memory & processing since the number of links to check grows substantially, but it finds the shortest path (Dijkstra's algorithm for an unweighted graph).

***

### Instructions for running
The `wikiracer.py` script takes a command lind argument in the form of a JSON object. The JSON object must have a `"start"` name with a string value of the starting Wikipedia article and an `"end"` name with a string value of the ending Wikipedia article.

```
python wikiracer.py '{"start": "https://en.wikipedia.org/wiki/Malaria", "end": "https://en.wikipedia.org/wiki/Geophysics"}
```

#### Error Handling
The script checks that `"start"` and `"end"` links are valid for performing a shortest path search. Valid means that:
+ `"start"` and `"end"` are existing Wikipedia pages
+ `"start"` and `"end"` are in the same language (there is no path between https://en.wikipedia.org/wiki/... and https://de.wikipedia.org/wiki/...)
+ `"start"` is not a "dead-end" page that contains no links to other Wikipedia articles.
  + The script should return a "No path" result anyways, but this check at least informs the user why.
+ `"end"` is not an "orphan" page with no links from other Wikipedia articles. Here, we determine that a page is an "orphan" if it is tagged with Wikipedia's "orphan" banner.

If `"end"` redirects to another page, the redirected page becomes WikiRacer's target destination. For example, for an `"end"` page of `"https://en.wikipedia.org/Bolshevik_Revolution"` WikiRacer will search for `"https://en.wikipedia.org/October_Revolution"` instead.

***

### Future Improvements
WikiRacer was developed for use on a personal laptop.  Further optimizations could be made for quicker runtime, including:
+ implementing a threaded recursive crawler to explore pages
+ pre-processing a [Wiki Dump](https://dumps.wikimedia.org/backup-index.html)
  + implementing the arc-flags algorithm to avoid exploring unnecessary paths
  + pre-computing all shortest paths between all possible pairs of articles (very computationally expensive)
+ using NLP techniques to re-order a page's links to visit based on semantic networks (to try to mimic human intuition)

***

### Example results and times
Times may vary due to internet connection.

**"Acoustic Kitty" --> "Animal Population Control"**
```
{
    "start": "https://en.wikipedia.org/wiki/Acoustic_Kitty",
    "end": "https://en.wikipedia.org/wiki/Animal_population_control",
    "path": [
        "https://en.wikipedia.org/wiki/Acoustic_Kitty",
        "https://en.wikipedia.org/wiki/Cat",
        "https://en.wikipedia.org/wiki/Animal_population_control"
    ]
}
Time: 0m 5.956s
```

**"Malaria"** --> **"Geophysics"**
```
{
    "start": "https://en.wikipedia.org/wiki/Malaria",
    "end": "https://en.wikipedia.org/wiki/Geophysics",
    "path": [
        "https://en.wikipedia.org/wiki/Malaria",
        "https://en.wikipedia.org/wiki/Compendium_of_Materia_Medica",
        "https://en.wikipedia.org/wiki/Geology",
        "https://en.wikipedia.org/wiki/Geophysics"
    ]
}
Time: 1m 49.279s
```
