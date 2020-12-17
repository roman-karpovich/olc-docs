Groups
======

group is just container for another form elements. think about them as structured fields

.. code-block:: python

    Group(
        'phone',
        TextField('number', required=True, validations=['phone_regex']),
        TextField('type', required=False),
        required=True, title='Phone',
    )


valid data for those group will be 

.. code-block:: json

    {
        "number": "1234567", "type": "mobile"
    }

group attributes:

- name
- title
- required
- repeatable *not implemented on frontend*
- extra - same as for fields
- styling - same as for fields

repeatable group can handle list of structures in responses. for example we can tweak previous code to save multiple phones instead of just one

.. code-block:: python

   Group(
        'phones',
        TextField('number', required=True, validations=['phone_regex']),
        TextField('type', required=False),
        required=True, title='Phones', 
        repeatable=True
    )


.. code-block:: json

    [
        {"number": "1234567", "type": "mobile"},
        {"number": "2234567", "type": "home"},
        {"number": "3234567"}
    ]


Styling
-------

TODO


Group validations
------------------

group validations can be supplied, but none of them already implemented neither on backend nor frontend. for more information check similar article in fields
