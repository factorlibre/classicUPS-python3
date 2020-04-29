ClassicUPS3: A Useful UPS Library
================================

ClassicUPS3 is an Apache2 Licensed wrapper around the UPS API for creating
shipping labels and fetching a package's tracking status. This library by no
means encompasses all of the UPS functionality, but it is suitable for some of
the most common shipping-related common tasks.


Features
--------

- Track delivery status of tracking number

- Create prepaid shipping labels in GIF or EPL (thermal printer) format


Installation
------------

Installation is easy:

.. code-block:: bash

    $ pip install ClassicUPS3

ClassicUPS3 depends on libxml2 and libxslt. On Ubuntu, the packages are
``libxml2-dev`` and ``libxslt-dev``.

Quickstart
----------

Create a UPSConnection object, which gives you access to common UPS methods:

.. code-block:: python

    from ClassicUPS3 import UPSConnection

    # Credentials obtained from the UPS website
    ups = UPSConnection(license_number,
                        user_id,
                        password,
                        shipper_number,  # Optional if you are not creating a shipment
                        debug=True)      # Use the UPS sandbox API rather than prod

Check the delivery date of a package.

.. code-block:: python

    tracking = ups.tracking_info('1Z12345E0291980793')
    print tracking.in_transit
    print tracking.delivered

Create shipment and save shipping label as GIF file:

.. code-block:: python

    from_addr = {
        'name': 'Google',
        'address1': '1600 Amphitheatre Parkway',
        'city': 'Mountain View',
        'state': 'CA',
        'country': 'US',
        'postal_code': '94043',
        'phone': '6502530000'
    }
    to_addr = {
        'name': 'President',
        'address1': '1600 Pennsylvania Ave',
        'city': 'Washington',
        'state': 'DC',
        'country': 'US',
        'postal_code': '20500',
        'phone': '2024561111'
    }
    package1 = {
        'packaging_type': '02', # Box (see http://www.ups.com/worldshiphelp/WS11/ENU/AppHelp/Codes/Package_Type_Codes.htm)
        'dimensions': {  # in inches
            'length': 1,
            'width': 4,
            'height': 9
        },
        'weight': 10 # in lbs
    }
    packages = [
        package1
    ]

    # Create the shipment. Use file_format='EPL' for a thermal-printer-compatible EPL
    shipment = ups.create_shipment(from_addr, to_addr, packages, 'standard', file_format='GIF')

    # Print information about our shipment
    print shipment.cost
    print shipment.tracking_number

    # Save the shipping label to print, email, etc
    # Warning if is a multipackage request only saves first label
    shipment.save_label(open('label.gif', 'wb'))
