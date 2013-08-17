.. _api_error_reporting:

Error Reporting
=================

Errors are reported with an HTTP status code accompanied by an error JSON object.  
The object contains the status, an internal error code, user-displayable message, and an internal developer message.

.. code-block:: guess

	HTTP/1.1 404 Not Found

	{
	  "error" : {
		"status" : 404,
		"code" : 0,
		"message" : "No sensor with that id",
		"developerMessage" : "user: mike sensor:gfhghjhj is not in the database"
	  }
	}

	
