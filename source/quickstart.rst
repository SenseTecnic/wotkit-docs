.. _quickstart:

Overview
==========

The WoTKit lets you quickly publish and find interesting data streams; from environmental sensors, GPS and on board data collection from vehicles, real time data feeds from mobile applications, building sensors, and internet-sourced content.  With the WoTKit you can easily add visualizations for display on a WoTKit dashboard and create applications using the WoTKit API.  This section will get you started with the WoTKit.  Once you are familiar with the WoTKit portal you can then add your own sensor to the system for use by the :ref:`WoTKit API <api-documentation>`.

Finding Sensors
===============

The WoTKit hosts many interesting sensor streams.  Some sensors on the system represent physical sensors and actuators such as temperature and light sensors connected to zigbee radios, or servo motors used to control things.  Other sensors host data pulled from web sites and external sensor systems such as the power use of buildings or ferry locations.

To find interesting public sensors, you need not create an account.  Simply hit the :wotkit:`Sensor Search <sensors>` page and use the UI there to find sensors.  The sensor search text box allows you to search by sensor name and description.  Click on tags to find sensors that use the selected tags.

Viewing a Sensor
===============

To sensor details and data click on the sensor in the list link in the map view in the sensor search page.  This will bring up a monitor page where you get information about the sensors such as its name, contributor, location, a table containing the data stream.  See the :wotkit:`Yellow Taxi <sensors/1/monitor>` for example.

To do more with the WoTKit, you'll need to create an account!

Create an Account
=================

To create your own sensors or add visualizations to your own dashboards, you'll need to create an account.  To do so, click on the log in button on the top right, then click on the *Create an Account* button.  Fill in the form and log in to the WoTKit.

Create a Widget
===============

Now that we're logged in, lets create a widget that displays sensor data on a dashboard using a line chart. 

    1. First, choose a sensor that you would like to visualize using the :wotkit:`sensor seaarch <sensors>` page.

    2. Type in 'light' in the search area.  Click on the sensor called 'Light Sensor' published by Sense Tecnic.

    3. In the :wotkit:`sensor monitor view </sensors/5/monitor>`, click on the *Visualizations and Widgets* tab on the lower half of the screen to view available visualizations for the sensor.  Lets select the visualization we want.  Feel free to try out available visualizations.

    4. Lets go with the *Line Graph* in the pop up.  Click on the *Create this Widget* button to create a widget.

The widget will appear in the :wotkit:`widget list <widgets>`.  To add it to your default dashboard, click on the *Add to Dashboard* button beside the widget.

The Widget will appear on your :wotkit:`dashboard <dashboards>`.  Feel free to move and resize the widget where you like.

Adding your own Sensor
======================

todo

Where to go from here
=====================

todo