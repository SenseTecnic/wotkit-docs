.. _quickstart:

Overview
========

The WoTKit lets you quickly publish, find and use interesting data streams in quick visualizations and your own applications;
from environmental sensors, GPS and on board data collection from vehicles, real time data feeds from mobile applications,
building sensors, and internet-sourced content.  With the WoTKit you can easily add visualizations for display on a WoTKit
dashboard and create applications using the WoTKit API.

This quick start tutorial will get you started with the WoTKit.

Finding Sensors
===============

The WoTKit hosts many interesting sensor streams.  Some sensors on the system represent physical sensors and actuators such as temperature and light sensors connected to zigbee radios, or servo motors used to control things.  Other sensors host data pulled from web sites and external sensor systems such as the power use of buildings or ferry locations.

To find interesting public sensors, you need not create an account.  Simply hit the :wotkit:`Sensor Search <sensors>` page and use the UI there to find sensors.  The sensor search text box allows you to search by sensor name and description.  Click on tags to find sensors that use the selected tags.

Viewing a Sensor
================

To sensor details and data click on the sensor in the list link in the map view in the sensor search page.  This will bring up a monitor page where you get information about the sensors such as its name, contributor, location, a table containing the data stream.  See the :wotkit:`Yellow Taxi <sensors/1/monitor>` for example.

To do more with the WoTKit, you'll need to create an account!

Create an Account
=================

To create your own sensors or add visualizations to your own dashboards, you'll need to create an account.  To do so, click on the log in button on the top right, then click on the *Create an Account* button.  Fill in the form and log in to the WoTKit.

Create a Widget
===============

Now that we're logged in, lets create a widget that displays sensor data on a dashboard using a line chart.

    1. First, choose a sensor that you would like to visualize using the :wotkit:`sensor search <sensors>` page.

    2. Type in 'light' in the search area.  Click on the sensor called 'Light Sensor' published by Sense Tecnic.

    3. In the :wotkit:`sensor monitor view </sensors/5/monitor>`, click on the *Visualizations and Widgets* tab on the lower half of the screen to view available visualizations for the sensor.  Lets select the visualization we want.  Feel free to try out available visualizations.

    4. Lets go with the *Line Graph* in the pop up.  Click on the *Create this Widget* button to create a widget.

The widget will appear in the :wotkit:`widget list <widgets>`.  To add it to your default dashboard, click on
the *Add to Dashboard* button beside the widget.

The Widget will appear on your :wotkit:`dashboard <dashboards>`.  Feel free to move and resize the
widget where you like.

Adding your own Sensor
======================

To add your own sensors to the WoTkit, you will first use the UI to create a sensor, create a key to generate credentials
for your sensor script to send data using the WoTKit API, then run your script to send data to the WoTKit.

    1. Create a sensor by clicking on the Sensors tab in the navigation bar to take you to the :wotkit:`Sensor Search <sensors>` page.  Click on the *New Sensor* button in the top right.

    2. Fill in the new sensor form.  Lets call it 'Test Sensor' with the name 'test-sensor'.  Click on the map to set a location for your sensor.

    3. Once you've filled in the form, you can view the :wotkit:`monitor page <sensors/test-sensor/monitor>` for that sensor.

At this point you've created a resource on the wotkit for your sensor.  Now it is time to create a key to use in your
sensor scripts to send data to the WoTKit using the API.

    1. Create an API key by clicking on the Keys button in the navigation bar to take you to the :wotkit:`Keys <keys>` page.

    2. Click on the *New Key* button in the top right.

    3. Fill in the new key form.  Lets call the key a 'Test Key' since we'll only use it for our test sensors.

Now that we've created a sensor resource and a key, lets write a script to send data to our sensor.  Lets start with
something simple like sending a random value to the sensor using Python.

Here's the code:

.. code-block:: python

    import random
    import time
    import datetime
    import urllib
    import urllib2
    import base64

    KEY_ID = 'PASTE_YOUR_KEY_ID_HERE'
    KEY_PASS = 'PASTE_YOUR_KEY_PASSWORD_HERE'

    if __name__ == '__main__':

        random.seed(time.time())

        # encode our key id and password
        base64string = base64.encodestring('%s:%s' % (KEY_ID, KEY_PASS))[:-1]

        # the URL for our sensor
        url = 'http://wotkit.sensetecnic.com/api/sensors/test-sensor/data'

        while 1:

            # get value from the sensor, in this case we'll just generate a random number
            value = random.randint(0,100)

            datafields = [('value','%d' % value)]

            params = urllib.urlencode(datafields)

            headers = {
                'User-Agent': 'httplib',
                'Content-Type': 'application/x-www-form-urlencoded',
                'Authorization': "Basic %s" % base64string
            }

            req = urllib2.Request(url,params,headers)
            try:
                result = urllib2.urlopen(req)

            except urllib2.URLError, e:
                print "error", e


            print 'random value sent: %d' % (value)

            time.sleep(2.0)

Be sure to paste your generated key id and password into the variables above and make sure the sensor name is the one
you chose for your sensor in the URL (we suggested 'test-sensor').

Now if all goes well, the script will send a random value to the wotkit every 2 seconds.  View the :wotkit:`monitor page <sensors/test-sensor/monitor>` to see the new data added to the data table below in near real time.  Click on the 'Visualizations and Widgets' tab to visualize the data
with line charts and graphs.

Where to go from here
=====================

Consult the :doc:`../index` for more information on using the WoTKit portal.

To create your own WoTKit applications, register sensors dynamically and take advantage of the WoTKit platform with your own applications, consult the :doc:`../api_v1/index`.

