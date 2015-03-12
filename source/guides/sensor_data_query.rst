.. _api_sensor_data_query:

.. index:: Querying Sensor Data

====================
Querying Sensor Data
====================

WoTKit provides flexibility in how you want to query your data.  In this
section, we walk through the different ways of building a query to get
sensor data out of wotkit. The queries are constructed using query parameters
which you append to a URL endpoint.

Typically applications will need to query for raw time-series
data of a sensor or group of sensors. There are two different types of queries:
:ref:`recent-query-label` and :ref:`time-range-query-label`.

The following document will walk through some examples of how to take advantage
of *Recent Queries* and *Time Range Queries*

.. _recent-query-label:

Recent Queries
--------------

To query for recent data, the API provides parameters for you to either:

  1) get *n* most recent sensor_data

  2) get sensor_data since *t* milliseconds in the past
  
In this section we'll dive in quickly and briefly show an example of
:ref:`recent-n-label` and :ref:`recent-t-label`.

.. _recent-n-label:

Recent Num Queries
^^^^^^^^^^^^^^^^^^

By default, the data endpoint will return the 1000 most recent queries. Try it
using a URL like this:

.. code-block:: javascript

  http://wotkit.sensetecnic.com/api/v2/sensors/sensetecnic.mule1/data


The response should look similar to the following:

.. code-block:: javascript
  :linenos:

  {
    "numFound": 100564,
    "data": [
      {
        "id": 47902511,
        "timestamp": "1398698531445",
        "timestamp_iso": "2013-11-29T00:46:36.056Z",
        "sensor_id": 1,
        "sensor_name": "sensetecnic.mule1",
        "value": 69,
        "lng": -123.17608,
        "lat": 49.14103
      },
      {
        "id": 47902514,
        "timestamp": "1398698531445",
        "timestamp_iso": "2013-11-29T00:46:39.556Z",
        "sensor_id": 1,
        "sensor_name": "sensetecnic.mule1",
        "value": 52,
        "lng": -123.17599,
        "lat": 49.13919
      },
      ... // more data
    ],
    "query": {
      "limit": 1000,
      "recent_n": 1000
    }
  }

The data is returned in JSON. Generally, all list responses are returned in this
container to aid paging and debugging.

.. list-table::
  :widths: 15, 40
  :header-rows: 1

  * - Field
    - Description
  * - numFound
    - The total number of elements matching this query (In version > 1.9.0 *numFound* is deprecated showing a value of 0)
  * - data
    - The enclosed sensor_data. Always sorted from oldest to newest timestamp
  * - query
    - Contains the interpreted query from the request. For debugging.
  * - metadata
    - Extra information. Depends on use case.


The query field is particularly interesting because it tells you how the query
was interpreted. In this case, the query has a **limit** of *1000*
and a **recent_n** of *1000*. A recent_n query fetches the **n** most recent
items. This is useful when API users want to peek at the recent data without
having to construct complex queries.

In essence, the query we ran is a convenient default for the explicit version:

.. code-block:: javascript

  http://wotkit.sensetecnic.com/api/v2/sensors/sensetecnic.mule1/data?limit=1000&recent_n=1000

Next we can try a recent_t query, which looks up the timestamp

.. _recent-t-label:

Recent Time Queries
^^^^^^^^^^^^^^^^^^^
Recent Time are very similar to Recent Num Queries. The difference is that
Recent Num Queries look at data count i.e. the last 10 elements, or the last 50
elements. Recent Time queries look at the timestamp instead. So, it's useful for
where we're interested in the elements from the last hour, or the 12 hours.

**Request**

.. code-block:: javascript

  http://wotkit.sensetecnic.com/api/v2/sensors/sensetecnic.mule1/data?recent_t=10000

**Response**

.. code-block:: javascript
  :linenos:

  {
    "numFound": 0,
    "data": [
        {
            "id": 47967438,
            "timestamp": "1398698531445",
            "timestamp_iso": "2013-11-29T18:34:09.557Z",
            "sensor_id": 1,
            "sensor_name": "sensetecnic.mule1",
            "value": 62,
            "lng": -123.14509,
            "lat": 49.186
        },
        {
            "id": 47967445,
            "timestamp": "1398698531445",
            "timestamp_iso": "2013-11-29T18:34:13.059Z",
            "sensor_id": 1,
            "sensor_name": "sensetecnic.mule1",
            "value": 53,
            "lng": -123.1454,
            "lat": 49.18565
        },
        {
            "id": 47967446,
            "timestamp": "1398698531445",           
            "timestamp_iso": "2013-11-29T18:34:16.557Z",
            "sensor_id": 1,
            "sensor_name": "sensetecnic.mule1",
            "value": 67,
            "lng": -123.14844,
            "lat": 49.18323
        }
    ],
    "query": {
        "limit": 1000,
        "recent_t": 10000
    }
  }


Looking at the *query* field this time, we can see it was interpreted as a
recent_t query. The query looked for items up to 10 seconds ago (10000
milliseconds). You can verify this by inspecting the timestamp of the data.

.. note::

  When accessing WoTKit anonymously, the date string is set to UTC. When accessing
  it using an api-key the timezone will be set based on the account's settings.

We've just shown you how to run both **Recent Queries**. One parameter to make
note of is the limit parameter. At the moment, limit is capped at 1000 -- which
restricts how much data you get in **recent_n** and **recent_t** queries. To overcome
this we will look into paging through historical data next.

.. _time-range-query-label:

Time Range Queries
------------------

At the end of the last section, we noted that there is a weakness in the recent
queries which limit your ability to sift through historical data. You can page
through historical data using the following query parameters. For the remainder
of this tutorial we will be working with the sensor ``rymndhng.sdq-test``.

.. _time-range-start-end-label:

Querying with Start and End
^^^^^^^^^^^^^^^^^^^^^^^^^^^
We'll start with a simple practical example. We have a defined starting time and
ending time where we want to get all the data in between. I want to know what
data was there between the iso timestamp ``2013-11-21T11:00:51.000Z`` and the iso
timestamp ``2013-11-29T22:59:54.862Z``, or from ``start: 1385031651000`` to
``end: 1385765994862``

.. Note::

  It is important to note that ``start`` is *exclusive* and ``end`` is
  *inclusive*. When using ``start=100`` and ``end=200`` the query will return: 

    ``start < sensor_data.timestamp <= end``


**Query Parameters**

.. list-table::
  :widths: 15, 40
  :header-rows: 1

  * - Query Parameter
    - Value
  * - start
    - 1385031651000 (2013-11-21T11:00:51.000Z)
  * - end
    - 1385765994862 (2013-11-29T22:59:54.862Z)
|

The API requires timestamp values to be in milliseconds, thus we can execute the
following request:

**Request**

.. code-block:: javascript

  http://wotkit.sensetecnic.com/api/v2/sensors/rymndhng.sdq-test/data?start=1385031651000&end=1385765994862

**Response**

.. code-block:: javascript
  :linenos:

  {
    "numFound": 0,
    "data": [
        {
            "id": 48232725,
            "timestamp": "1398698531445",
            "timestamp_iso": "2013-11-29T22:59:09.472Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "valua": 81
        },
        {
            "id": 48232726,
            "timestamp": "1398698531445",
            "timestamp_iso": "2013-11-29T22:59:09.472Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "valua": 53
        },
        {
            "id": 48232727,
            "timestamp": "1398698531445",            
            "timestamp_iso": "2013-11-29T22:59:19.633Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "valua": 0
        },
        {
            "id": 48232728,
            "timestamp": "1398698531445",
            "timestamp_iso": "2013-11-29T22:59:24.715Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "valua": 56
        },
        {
            "id": 48232729,
            "timestamp": "1398698531445",
            "timestamp_iso": "2013-11-29T22:59:54.862Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "value": 97
        }
    ],
    "query": {
        "end": "2013-11-29T22:59:54.862Z",
        "start": "2013-11-21T11:00:51.000Z",
        "limit": 1000
    }
  }

We can see that start/end was interpreted in the query between the start and end
points, specifically ``start < data[0].timestamp < ... < data[4].timestamp < end``.

Paging Through Data
^^^^^^^^^^^^^^^^^^^
The previous section illustrated a simple example returning a small range of 
elements. In real world applications the response of a query will often return
thousands of entries. In such case you might want to sift through a small ammount
of these entries at a time. Let's try querying a large range by using *start=0* and *end=2000000000000*. We will specify a `limit` of 3 to make the response
more comprehendable. 

**Query Parameters**

.. list-table::
  :widths: 15, 40
  :header-rows: 1

  * - Query Parameter
    - Value
  * - start
    - 0 (1970-01-01T00:00:00.000Z）
  * - end
    - 2000000000000 (2033-05-18T03:33:20.000Z)
  * - limit
    - 3
|

**Request**

.. code-block:: javascript

  http://wotkit.sensetecnic.com/api/v2/sensors/rymndhng.sdq-test/data?start=0&end=2000000000000&limit=3

**Response**

.. code-block:: javascript
  :linenos:

  {
      "numFound": 0,
      "data": [
          {
              "id": 48232722,
              "timestamp": "1398698531445",
              "timestamp_iso": "2013-11-21T10:58:51.000Z",
              "sensor_id": 531,
              "sensor_name": "rymndhng.sdq-test",
              "value": 6.7
          },
          {
              "id": 48232723,
              "timestamp": "1398698531445",
              "timestamp_iso": "2013-11-21T10:59:51.000Z",
              "sensor_id": 531,
              "sensor_name": "rymndhng.sdq-test",
              "value": 6.8
          },
          {
              "id": 48232724,
              "timestamp": "1398698531445",
              "timestamp_iso": "2013-11-21T11:00:51.000Z",
              "sensor_id": 531,
              "sensor_name": "rymndhng.sdq-test",
              "value": 6.9
          }
      ],
      "fields" [ /*an array of expected values*/ ],
      "query": {
          "end": "2033-05-18T03:33:20.000Z",
          "start": "1970-01-01T00:00:00.000Z",
          "limit": 3
      }
  }

In this query we have only asked for 3 elements. We can page data by setting the
parameter ``offset`` in our request. In our example, we can retrieve the next page 
by setting ``offset=data.size``, in our case 3: ``offset=3``. By specifying 
``offset = prev_offset + data.size`` we can paginate data in each subsequent request.
Now, let's retry the last query with an offset.

**Query Parameters**

.. list-table::
  :widths: 15, 40
  :header-rows: 1

  * - Parameter
    - Value
  * - start
    - 0 (same as before
  * - end
    - 2000000000000 (same as before)
  * - limit
    - 3
  * - offset
    - 3

**Request**

.. code-block:: javascript

  http://wotkit.sensetecnic.com/api/v2/sensors/rymndhng.sdq-test/data?start=0&end=2000000000000&limit=3&offset=3

**Response**

.. code-block:: javascript

  {
      "numFound": 0,
      "data": [
          {
              "id": 48232725,
              "timestamp": "1398698531445",
              "timestamp_iso": "2013-11-29T22:59:09.472Z",
              "sensor_id": 531,
              "sensor_name": "rymndhng.sdq-test",
              "valua": 81
          },
          {
              "id": 48232726,
              "timestamp": "1398698531445",
              "timestamp_iso": "2013-11-29T22:59:09.472Z",
              "sensor_id": 531,
              "sensor_name": "rymndhng.sdq-test",
              "valua": 53
          },
          {
              "id": 48232727,
              "timestamp": "1398698531445",
              "timestamp_iso": "2013-11-29T22:59:19.633Z",
              "sensor_id": 531,
              "sensor_name": "rymndhng.sdq-test",
              "valua": 0
          }
      ],
      "fields" [ /*an array of expected values*/ ],
      "query": {
          "offset": 3,
          "end": 2000000000000,
          "start": 0,
          "limit": 3
      }
}

Once again, looking at the query, we can now see that offset is specfied as 3.
We can also verify that an offset was used by looking at ``id`` and
``timestamp`` of the two responses. The **last** element of the first response
has ``id: 48232724`` and ``timestamp_iso: "2013-11-21T11:00:51.000Z"``. The
**first** element in the second response has ``id: 48232725`` and ``timestamp_iso:
"2013-11-29T22:59:09.472Z"``. You can easily verify that they are in sequence.


Advanced Time Range Queries
^^^^^^^^^^^^^^^^^^^^^^^^^^^
In general, using `start, end, offset` provides enough flexibility for most queries. However, sensors are allowed to have multiple data on the same timestamp. This can easily happen when historical data is ``PUT`` into the system. As a result several 
datapoints can have identical timestamps. What this means is that you cannot 
expect the timestamp value to be unique for a sensor data. 

To solve this we can use the parameters ``start_id`` and ``end_id`` for a more 
precise selection of start and end elements.

We'll start off with our first query
.. code-block:: javascript

  http://wotkit.sensetecnic.com/api/v2/sensors/rymndhng.sdq-test/data?start=0&end=2000000000000&limit=4

**Response**

.. code-block:: javascript

  {
    "numFound": 0,
    "data": [
        {
            "id": 48232722,
            "timestamp": "1385031531000",
            "timestamp_iso": "2013-11-21T10:58:51.000Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "value": 6.7
        },
        {
            "id": 48232723,
            "timestamp": "1385031531000",
            "timestamp_iso": "2013-11-21T10:59:51.000Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "value": 6.8
        },
        {
            "id": 48232724,
            "timestamp": "1385031651000",
            "timestamp_iso": "2013-11-21T11:00:51.000Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "value": 6.9
        },
        {
            "id": 48232725,
            "timestamp": "1385765949472",
            "timestamp_iso": "2013-11-29T22:59:09.472Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "valua": 81
        }
    ],
    "query": {
        "start": 0,
        "limit": 4
    }
  }

If we want to re-run this query in the future using the information we obtained 
in this query we will use the last item's timestamp "1385765949472" (2013-11-29T22:59:09.472Z) as the start value:

**Request**

.. code-block:: javascript

  http://wotkit.sensetecnic.com/api/v2/sensors/rymndhng.sdq-test/data?start=1385765949472&end=2000000000000&limit=4

**Response**

.. code-block:: javascript

  {
    "numFound": 0,
    "data": [
        {
            "id": 48232727,
            "timestamp": "1385765959633",
            "timestamp_iso": "2013-11-29T22:59:19.633Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "valua": 0
        },
        {
            "id": 48232728,
            "timestamp": "1385765964715",
            "timestamp_iso": "2013-11-29T22:59:24.715Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "valua": 56
        },
        {
            "id": 48232729,
            "timestamp": "1385765994862",
            "timestamp_iso": "2013-11-29T22:59:54.862Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "value": 97
        },
        {
            "id": 48232730,
            "timestamp": "1385766024862,","
            "timestamp_iso": "2013-11-29T23:00:24.862Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "value": 6.7
        }
    ],
    "fields": [/*Fields*/],
    "query": {
        "start": 1385765949472,
        "limit": 4
    }
  }

Everything looks fine doesn't it? Although the timestamps seem incremental there
is a problem we are unaware of. We have actually skyppped an element because of 
the existence of duplicate timestamps. If we run the following request querying 
the entire range this will become more aparent: 

**Request**

.. code-block:: javascript

  http://wotkit.sensetecnic.com/api/v2/sensors/rymndhng.sdq-test/data

**Response**

.. code-block:: javascript
  :emphasize-lines: 36,37,38,39,40,41,42,43
  :linenos:

  {
    "numFound": 0,
    "data": [
        {
            "id": 48232722,
            "timestamp": "1385031531000",
            "timestamp_iso": "2013-11-21T10:58:51.000Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "value": 6.7
        },
        {
            "id": 48232723,
            "timestamp": "1385031591000",
            "timestamp_iso": "2013-11-21T10:59:51.000Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "value": 6.8
        },
        {
            "id": 48232724,
            "timestamp": "1385031651000",
            "timestamp_iso": "2013-11-21T11:00:51.000Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "value": 6.9
        },
        {
            "id": 48232725,
            "timestamp": "1385765949472",
            "timestamp_iso": "2013-11-29T22:59:09.472Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "valua": 81
        },
        {   "_comment": "HIDDEN DUE TO DUPLICATE TIMESTAMP"
            "id": 48232726,
            "timestamp": "1385765949472",
            "timestamp_iso": "2013-11-29T22:59:09.472Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "valua": 53
        },
        {
            "id": 48232727,
            "timestamp": "1385765959633",
            "timestamp_iso": "2013-11-29T22:59:19.633Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "valua": 0
        },
        {
            "id": 48232728,
            "timestamp": "1385765964715",
            "timestamp_iso": "2013-11-29T22:59:24.715Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "valua": 56
        },
        {
            "id": 48232729,
            "timestamp": "1385765994862",
            "timestamp_iso": "2013-11-29T22:59:54.862Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "value": 97
        },
        {
            "id": 48232730,
            "timestamp": "1385766024862",
            "timestamp_iso": "2013-11-29T23:00:24.862Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "value": 6.7
        }
    ],
    "fields": [/*Fields*/],
    "query": {
        "limit": 100,
        "recent_n": 10
    }
  }

You can see that the highlighted lines for ``id: 48232726`` did not exist in either
of our previous queries. For example, in :ref:`time-range-start-end-label`, we performed a query for data after timestamp 1385765949472, but the element highlighted 
above was not returned. 

To solve this issue, we have introduced a new parameter ``start_id``. This
parameter can be used in conjuction with ``start`` to specify specify which data
element's id to start with. This works because sensor data are uniquely identified 
using a tuple ``(timestamp, id)``.

Let's rerun the second query with ``start_id: 48232725`` from the first query.

**Request**

.. code-block:: javascript

  http://wotkit.sensetecnic.com/api/v2/sensors/rymndhng.sdq-test/data?start=1385031651000&end=1385765994862&start_id=48232725

**Response**

.. code-block:: javascript

  {
      "numFound": 0,
      "data": [
          {
              "id": 48232726,
              "timestamp": "1385765949472",
              "timestamp": "2013-11-29T22:59:09.472Z",
              "sensor_id": 531,
              "sensor_name": "rymndhng.sdq-test",
              "value": 53
          },
          {
              "id": 48232727,
              "timestamp": "1385765959633",

              "timestamp": "2013-11-29T22:59:19.633Z",
              "sensor_id": 531,
              "sensor_name": "rymndhng.sdq-test",
              "value": 0
          },
          {
              "id": 48232728,
              "timestamp": "1385765964715",
              "timestamp": "2013-11-29T22:59:24.715Z",
              "sensor_id": 531,
              "sensor_name": "rymndhng.sdq-test",
              "value": 56
          },
          {
              "id": 48232729,
              "timestamp": "1385765994862",
              "timestamp": "2013-11-29T22:59:54.862Z",
              "sensor_id": 531,
              "sensor_name": "rymndhng.sdq-test",
              "value": 97
          }
      ],
      "query": {
          "start": 1385765949472,
          "limit": 4,
          "start_id": 48232725
      }
  }


When we used the parameter ``start_id`` we got a response with the element whose
`id: 48232726``. The ``start_id`` allowed us to filter ids greater than 48232726.
``end_id`` works the same way as ``start_id`` if you really need fine-grained 
control over the range of a data query.

.. _time-range-query-summary-label:

Summary of Time Range Data Query
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
We have learned all the parameters that can be used in a sensor query. But which
approach should you use?

  1) Without start_id or end_id, the query range is performed like this:

    .. code-block:: ruby

      start < data_ts <= end

    where ``data_ts`` is the sensor data's timestamp, and ``data_id`` 
    is the data's id element.

  2) With start_id and/or end_id, the query range adds extra checks near 
  the bounds like this:

    .. code-block:: ruby

      (start < data_ts <= end)
      OR (data_ts = start AND data_id > start_id)
      OR (data_ts = end   AND data_id <= end_id)

Below is a quicky summary of what each query parameter means:

.. list-table::
  :widths: 15, 15, 40
  :header-rows: 1

  * - Parameter
    - Type
    - Description
  * - ``start``
    - timestamp
    - The absolute starting point (in milliseconds since Jan 1, 1970).
  * - ``start_id``
    - id
    - The starting id of sensor_data at timestamp ``start``. Used for paging.
  * - ``end``
    - timestamp
    - The absolute ending timestamp (in milliseconds since Jan 1, 1970)
  * - ``end_id``
    - timestamp
    - The end id of sensor_data with timestamp ``end``. Used for paging.



Additional Sensor Data Query Recipes
------------------------------------
You can combine the information above in novel ways to query sensor data. 

1) Use start_id instead of start for start of query

  In the documentation, we used ``start_id`` alongisde ``start``, but actually,
  this is optional. If you use ``start_id`` without ``start``, WoTKit will lookup
  the ``timestamp`` of the element with id ``start_id``, and then use that
  as the starting timestamp.

2) Making Start Inclusive

  From :ref:`time-range-query-summary-label`, it shows the start range is
  exclusive. But, there is a way to make this inclusive. If you set ``start_id: 0``,
  it will make the data range inclusive.
