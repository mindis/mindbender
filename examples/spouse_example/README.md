# Mindbender for DeepDive's Spouse Example in DDlog

## Running DeepDive

Once you [install DeepDive](http://deepdive.stanford.edu/doc/basics/installation.html), use the following steps to run it.
You need a running PostgreSQL database configured in the `db.url` file.
See [DeepDive's tutorial](http://deepdive.stanford.edu/doc/basics/walkthrough/walkthrough.html) for more details.

```bash
cd examples/spouse_example
deepdive initdb
deepdive run
```

## Search

Next, you can bring up a full text search with the following two steps:

1. Populate the search index with the DeepDive data products.

    ```bash
    mindbender search update
    ```

2. Start the GUI

    ```bash
    mindbender search gui
    ```

Point your browser to <http://localhost:8000/> to start your search over the data produced by DeepDive.
See the [Elasticsearch manual on query string syntax](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-query-string-query.html#query-string-syntax) to learn how to formulate sophisticated query strings that can limit fields, do wildcards, fuzzy search, regular expressions, filtering, and grouping with boolean operators.


### DDlog Annotations

The magic behind all this is DDlog annotations, e.g., `@extraction`, `@source`, `@key`, `@references`, `@searchable`, `@navigable`.
The search index and user interface is generated by the annotations you put in the DDlog program kept in the [`app.ddlog` file](https://github.com/HazyResearch/mindbender/blob/master/examples/spouse_example/app.ddlog).

The annotation language is compact and can be quite powerful for bringing up a sophisticated search tool with little effort.
Mindbender currently uses the DDlog annotations in the following ways:

1. Relations that are annotated with `@extraction` or `@source` are indexed and becomes searchable.
    * All other relations associated with `@references` and `@key` are nested under the searchable relation.
    * An `@extraction` relation should have `@references` columns to a `@source` relation.
2. All columns of the indexed relations are full-text searchable.
3. `@searchable` columns are used for presenting the search results with highlights.
4. `@navigable` columns and `@searchable` columns are used for faceted navigation.

The annotation language is still undergoing development to allow expressing more sophisticated ways of faceted navigation and aggregation as well as presentation of the mentions, entities, and relationships extracted by DeepDive.
For latest progress, see the [design document for DDlog annotation language for search](https://docs.google.com/document/d/1XGGcfiVRQEKh2sQDJSTYFYX1j9ucNojq51WzJjJu-W0/edit?usp=sharing).



## Dashboard

To produce a set of reports containing descriptive statistics about the latest DeepDive result, use the following command:

```bash
mindbender snapshot
```

You can share the search GUI to see the dashboard (using the dropdown menu on the top-left), but to quickly bring up a GUI just for the dashboard without search capability, run:

```bash
mindbender dashboard
```

Dashboard snapshot requires [some manual configuration and customization](https://github.com/HazyResearch/mindbender/blob/master/dashboard/README.md#readme) of the SQL queries used in the report templates.
Our plan is to also abstract this away from the user with DDlog annotations.


## Mindtagger

For the moment, Mindtagger requires a bit more manual configuration and data preparation.
See [DeepDive documentation about data labeling](http://deepdive.stanford.edu/doc/basics/labeling.html) and the [examples in mindbender source tree](https://github.com/HazyResearch/mindbender/tree/master/examples/labeling).
Once you have the task directories configured, Mindtagger GUI can be brought up using the following command:

```bash
mindbender tagger **/mindtagger.conf
```

Having the flexibility to use it towards a variety of tasks is great, but it's even greater if that could be done with minimal effort.
It'll soon be possible to generate standard Mindtagger tasks from DDlog annotations as well.
