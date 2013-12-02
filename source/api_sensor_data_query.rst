.. _api_sensor_data_query:

.. index:: Querying Sensor Data

====================
Querying Sensor Data
====================

WoTKit provides flexibility in how you want to query your data.  In the following
section, we walk through the different ways of building a query to get
sensor data out of wotkit. The queries are constructed using query parameters
which you append to a URL endpoint.

Generally, the use cases of the data api is to query for the raw time-series
data of a sensor or group of sensors. There are two different types of queries:
:ref:`recent-query-label` and :ref:`time-range-query-label`.

:ref:`recent-query-label` are used to easily look at recent information. The API
provides parameters for you to either:

  1) get *n* most recent sensor_data

  2) get sensor_data since *t* milliseconds in the past


:ref:`time-range-query-label` are useful for going through data in the past.
    These queries allow you to page through data by specifying a start & end
    point in time.

The following document will walk through some examples of how to take advantage
of *Recent Queries* and *Time Range Queries*

.. _recent-query-label:

Recent Queries
--------------
In this section we'll dive in quickly and briefly show an example of
:ref:`recent-n-label` and :ref:`recent-t-label`.

.. _recent-n-label:

Recent Num Queries
^^^^^^^^^^^^^^^^^^

By default, the data endpoint will return the 100 most recent queries. Try it
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
        "timestamp": "2013-11-29T00:46:36.056Z",
        "sensor_id": 1,
        "sensor_name": "sensetecnic.mule1",
        "value": 69,
        "lng": -123.17608,
        "lat": 49.14103
      },
      {
        "id": 47902514,
        "timestamp": "2013-11-29T00:46:39.556Z",
        "sensor_id": 1,
        "sensor_name": "sensetecnic.mule1",
        "value": 52,
        "lng": -123.17599,
        "lat": 49.13919
      },
      ... // more data
    ],
    "query": {
      "limit": 100,
      "recent_n": 100
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
    - The total number of elements matching this query
  * - data
    - The enclosed sensor_data. Always sorted from oldest to newest timestamp
  * - query
    - Contains the interpreted query from the request. For debugging.
  * - metadata
    - Extra information. Depends on use case.


The query field is particularly interesting because it tells you how the query
was interpreted. In this case, the query has a **limit** of *100*
and a **recent_n** of *100*. A recent_n query fetches the **n** most recent
items. This is useful when API users want to peek at the recent data without
having to construct complex queries.

In essence, the query we ran is a convenient default for the explicit version:

.. code-block:: javascript

  http://wotkit.sensetecnic.com/api/v2/sensors/sensetecnic.mule1/data?limit=100&recent_n=100

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
    "numFound": 3,
    "data": [
        {
            "id": 47967438,
            "timestamp": "2013-11-29T18:34:09.557Z",
            "sensor_id": 1,
            "sensor_name": "sensetecnic.mule1",
            "value": 62,
            "lng": -123.14509,
            "lat": 49.186
        },
        {
            "id": 47967445,
            "timestamp": "2013-11-29T18:34:13.059Z",
            "sensor_id": 1,
            "sensor_name": "sensetecnic.mule1",
            "value": 53,
            "lng": -123.1454,
            "lat": 49.18565
        },
        {
            "id": 47967446,
            "timestamp": "2013-11-29T18:34:16.557Z",
            "sensor_id": 1,
            "sensor_name": "sensetecnic.mule1",
            "value": 67,
            "lng": -123.14844,
            "lat": 49.18323
        }
    ],
    "query": {
        "limit": 100,
        "recent_t": 10000
    }
  }


Looking at the *query* field this time, we can see it was interpreted as a
recent_t query. The query looked for items up to 10 seconds ago (10000
milliseconds). You can verify this by inspecting the timestamp of the data.

.. note::

  When accessing WoTKit anonymously, the date string is set to UTC. If you
  access it an api-key, the timezone will be set based on the account's settings.


We've just shown you how to run both **Recent Queries**. One parameter to make
note of is the limit parameter. At the moment, limit is capped at 100 -- which
restricts how much data you get in **recent_n** and **recent_t** queries. To overcome
this we will look into paging through historical data next.


.. _time-range-query-label:

Time Range Queries
------------------

At the end of the last section, we noted that there is a weakness in the recent
queries which limit your ability to sift through historical data. So, to look
through historical data, you can page through historical data using the
following query parameters. For the remainder of this, we will be working with
the sensor ``rymndhng.sdq-test``.

.. _time-range-start-end-label:

Querying with Start and End
^^^^^^^^^^^^^^^^^^^^^^^^^^^
We'll start with a simple practical example. We have a defined starting time and
ending time where we want to get all the data in between. I want to know what
data was there between ``start: "2013-11-21T11:00:51.000Z"`` to
``end: "2013-11-29T22:59:54.862Z"``

.. Note::

  It's important to note that ``start`` is *exclusive* and ``end`` is
  *inclusive*. i.e. for ``start=100`` and ``end=200``, then the query does the
  following:

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

Translating those two strings to milliseconds,
we end up with the request below. Execute it and follow the response.

**Request**

.. code-block:: javascript

  http://wotkit.sensetecnic.com/api/v2/sensors/rymndhng.sdq-test/data?start=1385031651000&end=1385765994862

**Response**

.. code-block:: javascript
  :linenos:

  {
    "numFound": 5,
    "data": [
        {
            "id": 48232725,
            "timestamp": "2013-11-29T22:59:09.472Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "valua": 81
        },
        {
            "id": 48232726,
            "timestamp": "2013-11-29T22:59:09.472Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "valua": 53
        },
        {
            "id": 48232727,
            "timestamp": "2013-11-29T22:59:19.633Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "valua": 0
        },
        {
            "id": 48232728,
            "timestamp": "2013-11-29T22:59:24.715Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "valua": 56
        },
        {
            "id": 48232729,
            "timestamp": "2013-11-29T22:59:54.862Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "value": 97
        }
    ],
    "query": {
        "end": 1385765994862,
        "start": 1385031651000,
        "limit": 100
    }
  }

Once again, let's look at the query parameter in the response to see what was
interpreted. We can see that start/end was interpreted in the query. Inspect the
timestamps of of both data points, we can see it's between the start/end points,
specifically ``start < data[0].timestamp < ... < data[4].timestamp < end``.

Paging Through Data
^^^^^^^^^^^^^^^^^^^
In the previous section, we gave a very naive example. In this case, only two
elements were in the range and therefore all the relevent data was returned.
Very often this isn't the case -- and you may want to sift through thousands of
entries at a time. To do this, we enabled paging through data entries. We'll
also specify `limit` to 10 to make the Response more comprehenedable.

Let's try to query all the data by choosing ``start: 0`` and and a really large
``end: 2000000000000``.

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

**Request**

.. code-block:: javascript

  http://wotkit.sensetecnic.com/api/v2/sensors/rymndhng.sdq-test/data?start=0&end=2000000000000&limit=3

**Response**

.. code-block:: javascript
  :linenos:

  {
      "numFound": 9,
      "data": [
          {
              "id": 48232722,
              "timestamp": "2013-11-21T10:58:51.000Z",
              "sensor_id": 531,
              "sensor_name": "rymndhng.sdq-test",
              "value": 6.7
          },
          {
              "id": 48232723,
              "timestamp": "2013-11-21T10:59:51.000Z",
              "sensor_id": 531,
              "sensor_name": "rymndhng.sdq-test",
              "value": 6.8
          },
          {
              "id": 48232724,
              "timestamp": "2013-11-21T11:00:51.000Z",
              "sensor_id": 531,
              "sensor_name": "rymndhng.sdq-test",
              "value": 6.9
          }
      ],
      "query": {
          "end": 2000000000000,
          "start": 0,
          "limit": 3
      }
  }

So this time, first we look at the query parameter. As mentioned previously, the
limit is currently capped at 3. So how do we know if we there's more data? Well,
there is another field in the response which can help us: ``numFound``.
``numFound`` counts all the data found within the data range from start to end.
In this example, we know there's more data because ``data.length < numFound``.


Given this information, we can now continue paging data by setting ``offset``.
We can retrieve the next page by choosing ``offset = data.size``, in this case,
data.size is 10. Generally, we can page by specifying ``offset = prev_offset +
data.size``. We can also figure out if we're at the end of the data range
generally by testing that ``data.size + offset < numFound``.

Now, let's rerun the last query with an offset.

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
    - 10
  * - offset
    - 3

**Request**

.. code-block:: javascript

  http://wotkit.sensetecnic.com/api/v2/sensors/rymndhng.sdq-test/data?start=0&end=2000000000000&limit=3&offset=3

**Response**

.. code-block:: javascript

  {
      "numFound": 9,
      "data": [
          {
              "id": 48232725,
              "timestamp": "2013-11-29T22:59:09.472Z",
              "sensor_id": 531,
              "sensor_name": "rymndhng.sdq-test",
              "valua": 81
          },
          {
              "id": 48232726,
              "timestamp": "2013-11-29T22:59:09.472Z",
              "sensor_id": 531,
              "sensor_name": "rymndhng.sdq-test",
              "valua": 53
          },
          {
              "id": 48232727,
              "timestamp": "2013-11-29T22:59:19.633Z",
              "sensor_id": 531,
              "sensor_name": "rymndhng.sdq-test",
              "valua": 0
          }
      ],
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
has ``id: 48232724`` and ``timestamp: "2013-11-21T11:00:51.000Z"``. The
**first** element in the second response has ``id: 48232725`` and ``timestamp:
"2013-11-29T22:59:09.472Z"``. You can easily verify that they are in sequence.


Advanced Time Range Queries
^^^^^^^^^^^^^^^^^^^^^^^^^^^
In general, using `start, end, offset` provides enough flexibility. However,
sensors are allowed to have multiple data on the same timestamp. This can easily
happen when historical data is ``PUT`` into the system. In other words, you
cannot expect timestamp to be unique for sensor data (generally they are good
enough). So, we introduce the idea of ``start_id`` and ``end_id`` to allow
precise selection of start and end elements.

We'll start off with our first query
.. code-block:: javascript

  http://wotkit.sensetecnic.com/api/v2/sensors/rymndhng.sdq-test/data?start=0&limit=4

**Response**

.. code-block:: javascript

  {
    "numFound": 9,
    "data": [
        {
            "id": 48232722,
            "timestamp": "2013-11-21T10:58:51.000Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "value": 6.7
        },
        {
            "id": 48232723,
            "timestamp": "2013-11-21T10:59:51.000Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "value": 6.8
        },
        {
            "id": 48232724,
            "timestamp": "2013-11-21T11:00:51.000Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "value": 6.9
        },
        {
            "id": 48232725,
            "timestamp": "2013-11-29T22:59:09.472Z",
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

Now sometime in the future, we want to rerun the query using the information we
had previously. So, we'll use the last item's timestamp
(2013-11-29T22:59:09.472Z) as the start value.

**Request**

.. code-block:: javascript

  http://wotkit.sensetecnic.com/api/v2/sensors/rymndhng.sdq-test/data?start=1385765949472&limit=4

**Response**

.. code-block:: javascript

  {
    "numFound": 4,
    "data": [
        {
            "id": 48232727,
            "timestamp": "2013-11-29T22:59:19.633Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "valua": 0
        },
        {
            "id": 48232728,
            "timestamp": "2013-11-29T22:59:24.715Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "valua": 56
        },
        {
            "id": 48232729,
            "timestamp": "2013-11-29T22:59:54.862Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "value": 97
        },
        {
            "id": 48232730,
            "timestamp": "2013-11-29T23:00:24.862Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "value": 6.7
        }
    ],
    "query": {
        "start": 1385765949472,
        "limit": 4
    }
  }

Everything looks fine and dandy doesn't it? The timestamps are incremental, and
therefore all is well is it? Well, no it actually isn't. There's a problem which
we are unaware of. We've actually skipped an element because of duplicate
timestamps.

Run this request which querys the entire range and look at the data.

**Request**

.. code-block:: javascript

  http://wotkit.sensetecnic.com/api/v2/sensors/rymndhng.sdq-test/data

**Response**

.. code-block:: javascript
  :emphasize-lines: 32,33,34,35,36,37,38
  :linenos:

  {
    "numFound": 9,
    "data": [
        {
            "id": 48232722,
            "timestamp": "2013-11-21T10:58:51.000Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "value": 6.7
        },
        {
            "id": 48232723,
            "timestamp": "2013-11-21T10:59:51.000Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "value": 6.8
        },
        {
            "id": 48232724,
            "timestamp": "2013-11-21T11:00:51.000Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "value": 6.9
        },
        {
            "id": 48232725,
            "timestamp": "2013-11-29T22:59:09.472Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "valua": 81
        },
        {
            "id": 48232726,
            "timestamp": "2013-11-29T22:59:09.472Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "valua": 53
        },
        {
            "id": 48232727,
            "timestamp": "2013-11-29T22:59:19.633Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "valua": 0
        },
        {
            "id": 48232728,
            "timestamp": "2013-11-29T22:59:24.715Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "valua": 56
        },
        {
            "id": 48232729,
            "timestamp": "2013-11-29T22:59:54.862Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "value": 97
        },
        {
            "id": 48232730,
            "timestamp": "2013-11-29T23:00:24.862Z",
            "sensor_id": 531,
            "sensor_name": "rymndhng.sdq-test",
            "value": 6.7
        }
    ],
    "query": {
        "limit": 100,
        "recent_n": 10
    }
  }

The highlighted lines for ``id: 48232726`` did not exist in either of our
queries. In :ref:`time-range-start-end-label`, we specified the second query did
exactly what you asked for: *Query sensor data after timestamp 1385765949472
limit 3*. So, to solve this, we introduce a new parameter ``start_id``. This
parameter can be used in conjuction with ``start`` to specify specify which data
element's id to start with. Essentially, sensor_data are uniquely identified
using this tuple ``(timestamp, id)``. So, let's rerun the second query with
``start_id: 48232725`` from the first query.


**Request**

.. code-block:: javascript

  http://wotkit.sensetecnic.com/api/v2/sensors/rymndhng.sdq-test/data?start=1385765949472&limit=4&start_id=48232725

**Response**

.. code-block:: javascript

  {
      "numFound": 5,
      "data": [
          {
              "id": 48232726,
              "timestamp": "2013-11-29T22:59:09.472Z",
              "sensor_id": 531,
              "sensor_name": "rymndhng.sdq-test",
              "valua": 53
          },
          {
              "id": 48232727,
              "timestamp": "2013-11-29T22:59:19.633Z",
              "sensor_id": 531,
              "sensor_name": "rymndhng.sdq-test",
              "valua": 0
          },
          {
              "id": 48232728,
              "timestamp": "2013-11-29T22:59:24.715Z",
              "sensor_id": 531,
              "sensor_name": "rymndhng.sdq-test",
              "valua": 56
          },
          {
              "id": 48232729,
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


There, we got the response with ``id: 48232726``. The ``start_id`` allowed us to
filter ids greater than 3. ``end_id`` works the same way as ``start_id`` if you
really need fine-grained control over the range of a data query.

.. _time-range-query-summary-label:

Summary of Time Range Data Query
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
With all the information given, we can really condense the query parameters into
the following query. ``data_ts`` is the sensor data's timestamp, and ``data_id``
is the data's id element.

Without start_id or end_id, the query range is done like this.

.. code-block:: ruby

  start < data_ts <= end

With start_id and/or end_id, the query range adds extra checks near the bounds

.. code-block:: ruby

  (start < data_ts <= end)
  OR (data_ts = start AND data_id > start_id)
  OR (data_ts = end   AND data_id <= end_id)

Below is a quicky summary of what query parameter means:

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



Sensor Data Query Recipes
-------------------------
In this section, we will highlight some novel ways of combining the information
above to query the data.

Use start_id instead of start for start of query
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
In the documentation, we used ``start_id`` alongisde ``start``, but actually,
this is optional. If you use ``start_id`` without ``start``, we will actually
lookup the ``timestamp`` of the element with id ``start_id``, and then use that
as the starting timestamp.

Making Start Inclusive
^^^^^^^^^^^^^^^^^^^^^^
From :ref:`time-range-query-summary-label`, it shows the start range is
exclusive. But, there is a way to make this inclusive. If you set ``start_id: 0``,
it will make the data range inclusive.
