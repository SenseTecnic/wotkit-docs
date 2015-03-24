.. _api_sensor_groups:


.. _sensor-groups-label:

Sensor Groups
=============
Sensor Groups are used to logically organize related sensors. Any sensor can be a member of many groups.

Currently, all Sensor Groups have **private** visibility, and **only** the **owner** (creator) can add/remove sensors from the group, or make a group **public**.

Sensor Groups can be manipulated using a REST API in the following section


.. _sensor-group-format-label:

Sensor Group Format
-------------------
All request body and response bodies use JSON. The following fields are present:


.. list-table::
  :widths: 5, 5, 5, 30
  :header-rows: 1

  * - 
    - Field Name
    - Type
    - Notes
  * - (*REQUIRED*)
    - **id**
    - Integer
    - The id contains a unique number which is used to identify the group
  * - (*REQUIRED*) 
    - **name**
    - String[4,50]
    - The name is a system-unique string identifier for the group. Names must be lowercase containing alphanumeric, underscores or hyphens ``[a-z0-9_-]``. The first character **must** be an alphabetic character
  * - (*OPTIONAL*)
    - **longName**
    - String[,255]
    - A readable name used for visual interfaces.
  * - (*READ-ONLY*)
    - **owner**
    - String[4,50]
    - The name of the group's owner. This field is set by the system and cannot be modified.
  * - (*OPTIONAL*)
    - **description**
    - String[,255]
    - A simple description of the group
  * - (*OPTIONAL*)
    - **imageUrl**
    - String[,255]
    - A string url to an image which can be used to represent this group
  * - (*OPTIONAL*)
    - **sensors**
    - Array[Sensor]
    - Contains a JSON list of sensors. This field is only useful for viewing sensors. To append/remove sensors from Sensor Groups, refer to :ref:`sensor-groups-add-sensor-label`.

An example of a Sensor Group JSON would be as follows:

.. code-block:: python

	{
	  "id": 602,
	  "name": "test",
	  "longName": "test",
	  "description": "test",
	  "tags": [
	    "group",
	    "test"
	  ],
	  "imageUrl": "",
	  "latitude": 49.25,
	  "longitude": -123.1,
	  "visibility": "PUBLIC",
	  "owner": "sensetecnic",
	  "lastUpdate": "1970-01-01T00:00:00.000Z",
	  "created": "2014-03-27T23:29:51.479Z",
	  "metadata": {
	    "meta": "data"
	  },
	  "childCount": 0,
	  "things": [],
	  "thingType": "GROUP"
	}

.. _list-groups-label:

List Groups
-----------
Provides a list of groups on the system as an array using the JSON format specified in :ref:`sensor-group-format-label`

.. list-table::
  :widths: 10, 80

  * - **URL**
    - :wotkit-api-v1:`groups/`
  * - **Method**
    - GET
  * - **Returns**
    - **200 OK** if successul. A JSON object in the response body containing a list of groups.

.. admonition:: example

  .. parsed-literal::
    curl --user {id}:{password} --request GET ':wotkit-api-v1:`groups`'


.. _view-sensor-group-label:

Viewing a Single Sensor Group
-----------------------------
Similar to listing a group, but retrieving only a single sensor. Replace ``{group-name}`` with the group's ``{id}`` integer or ``{owner}.{name}`` string. The API accepts both formats

.. list-table::
  :widths: 10, 80

  * - **URL**
    - :wotkit-api-v1:`groups/{group-name}`
  * - **Method**
    - GET
  * - **Returns**
    - **200 OK** if successful. A JSON object in the response body describing the sensor.

.. admonition:: example

  .. parsed-literal::
    curl --user {id}:{password} --request GET ':wotkit-api-v1:`groups`/{group-name}'


.. _create-sensor-group-label:

Creating a Sensor Group
-----------------------
To create a sensor group, append the Sensor Group contents following :ref:`sensor-group-format-label`.

On creation, the **id** and **owner** fields are **ignored** because they are system generated.

.. list-table::
  :widths: 10, 80

  * - **URL**
    - :wotkit-api-v1:`groups`
  * - **Method**
    - POST
  * - **Returns**
    - **204 No Content** if successful; **409 Conflict** if a sensor with the same name exists.


.. _modify-sensor-group-fields-label:

Modifying Sensor Group Fields
-----------------------------
Modifying is similar to creation, the content is placed in the response body

Again, the **id** and **owner** fields in the JSON object are **ignored** if they are modified. The Sensor Group is specified by substituting ``{group-name}`` in the URL with either ``group.id`` or ``group.name``. The API accepts both formats.

.. list-table::
  :widths: 10, 80

  * - **URL**
    - :wotkit-api-v1:`groups/{group-name}`
  * - **Method**
    - PUT
  * - **Returns**
    - **204 No Content** if successful; **401 Unauthorized** if user has no permissions to edit group.


.. _delete-sensor-group-label:

Deleting a Sensor Group
-----------------------
Deleting a Sensor Group is fairly trivial, assuming you are the owner of the group.
A response body is unnecessary.

.. list-table::
  :widths: 10, 80

  * - **URL**
    - :wotkit-api-v1:`groups/{group-name}`
  * - **Method**
    - DELETE
  * - **Returns**
    - **204 No Content** if successful; **401 Unauthorized** if user has no permissions to edit group.



.. _sensor-groups-add-sensor-label:

Adding a Sensor to Sensor Group
-------------------------------
This is done by invoking the URL by replacing the specified parameters where
``{group-name}`` can be ``group.id`` or ``group.name``. ``{sensor-id}`` should
be ``sensor.id``.


.. list-table::
  :widths: 10, 80

  * - **URL**
    - :wotkit-api-v1:`groups/{group-name}/sensors/{sensor-id}`
  * - **Method**
    - POST
  * - **Returns**
    - **204 No Content** if successful; **400** if sensor is already a member of sensor group; **401 Unauthorized** if user is unauthorized to edit group.


.. _sensor-groups-remove-sensor-label:

Removing a Sensor from Sensor Group
-----------------------------------

The format is the same as :ref:`sensor-groups-add-sensor-label` except replacing ``method`` with ``DELETE``

.. list-table::
  :widths: 10, 80

  * - **URL**
    - :wotkit-api-v1:`groups/{group-name}/sensors/{sensor-id}`
  * - **Method**
    - DELETE
  * - **Returns**
    - **204 No Content** if successful; **401 Unauthorized** if user is unauthorized to edit group.

