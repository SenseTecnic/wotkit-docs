.. _api_error_reporting:


.. _error-reporting-label:

Error Reporting
=================

Errors are reported with an HTTP status code accompanied by an error JSON object.  
The object contains the status, an internal error code, user-displayable message, and an internal developer message.

For example, when a sensor cannot be found, the following error is returned:

.. code-block:: guess

	HTTP/1.1 404 Not Found

	{
	    "error": {
	        "status": 404,
	        "code": 0,
	        "message": "No thing with that id or name",
	        "developerMessage": ["my_sensor"]
	    }
	}

	
