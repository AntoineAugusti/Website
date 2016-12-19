# Querying MongoDB

###Simple query example:
```
{
    "collection" : "my_collection",
    "query" : {
        "date" : {
            "$gt" : "ISODate("2015-01-15 11:41")",
        },
        "type" : 1
    },
    "fields" : {
        "_id" : 1,
        "name" : 2
    },
    "sort" : [
       {
            "name" : "date",
            "direction" : -1
       }
    ]
}
```
Live example on the demo instance: http://demo.redash.io/queries/394/source.

###Aggregation
Uses a syntax similar to the one used in PyMongo, however to support the correct order of sorting, it uses a regular list for the “$sort” operation that converts into a SON (sorted dictionary) object before execution.

Aggregation query example:

```
{
    "collection" : "things",
    "aggregate" : [
        {
            "$unwind" : "$tags"
        },
        {
            "$group" : {
                "_id" : "$tags",
                "count" : { "$sum" : 1 }
            }
        },
        {
            "$sort" : [
                {
                    "name" : "count",
                    "direction" : -1
                },
                {
                    "name" : "_id",
                    "direction" : -1
                }
            ]
        }
    ]
}
```
Live examples on the demo instance:

http://demo.redash.io/queries/393/source
http://demo.redash.io/queries/387/source

###MongoDB Extended JSON Support

We support MongoDB Extended JSON along with our own extension - $humanTime:

```
{
    "collection": "date_test",
    "query": {
        "lastModified": {
            "$gt": {
                "$humanTime": "3 years ago"
            }
        }
    },
    "limit": 100
}
```
It accepts human readable string like the above (“3 years ago”, “yesterday”, etc) or timestamps.

Live example on the demo instance: http://demo.redash.io/queries/2112/source.

###MongoDB Filtering
You can add filters to Mongo queries by projecting a column with the '::filter' keyword added on to the end.

```
{
    "collection": "zipcodes",
    "aggregate": [
        {
            "$project": {
                "_id": "$_id",
                "city": "$city",
                "loc": "$loc",
                "pop": "$pop",
                "state::filter": "$state"
            }
        }
    ]
}
```

The above example will show a 'State' column, and allow you to filter on this column.

Live example on the demo instance: http://demo.redash.io/queries/3523/source.
