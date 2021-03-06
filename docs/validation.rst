.. _validation:

Data Validation
===============
Data validation is provided out-of-the-box. Your configuration includes
a schema definition for every resource managed by the API. Data sent to the API
to be inserted/updated will be validated against the schema, and a resource
will only be updated if validation passes.

.. code-block:: console

    $ curl -d '[{"firstname": "bill", "lastname": "clinton"}, {"firstname": "mitt", "lastname": "romney"}]' -H 'Content-Type: application/json' http://eve-demo.herokuapp.com/people
    HTTP/1.1 201 OK

The response will contain a success/error state for each item provided in the
request:

.. code-block:: javascript

    {
        "_status": "ERR",
        "_error": "Some documents contains errors",
        "_items": [
            {
                "_status": "ERR",
                "_issues": {"lastname": "value 'clinton' not unique"}
            },
            {
                "_status": "OK",
            }
        ]
    ]

In the example above, the first document did not validate so the whole request
has been rejected. 

When all documents pass validation and are inserted correctly the response
status is ``201 Created``. If any document fails validation the response status
is ``422 Unprocessable Entity``, or any other error code defined by
``VALIDATION_ERROR_STATUS`` configuration.


Extending Data Validation
-------------------------
Data validation is based on the Cerberus_ validation system and it is therefore
extensible. As a matter of fact, Eve's data-layer itself extends Cerberus
validation, implementing the ``unique`` and ``data_relation`` constraints on
top of the standard rules (for MongoDB, Eve also adds the ``ObjectId`` data
type).

Custom Validation Rules
------------------------
Suppose that in your specific and very peculiar use case, a certain value can
only be expressed as an odd integer. You decide to add support for a new
``isodd`` rule to our validation schema. This is how you would implement
that (for SQLAlchemy, just replace ``eve.io.mongo`` with ``eve.io.sql``  and
``Validator`` with ``ValidatorSQL``):

.. code-block:: python

    from eve.io.sql import ValidatorSQL

    class MyValidator(ValidatorSQL):
        def _validate_isodd(self, isodd, field, value):
            if isodd and not bool(value & 1):
                self._error(field, "Value must be an odd number")

    app = Eve(validator=MyValidator)

    if __name__ == '__main__':
        app.run()

By subclassing the base validator class and then adding a custom
``_validate_<rulename>`` method, you extended the available :ref:`schema`
grammar and now the new custom rule ``isodd`` is available in your schema. You
can now do something like:

.. code-block:: python

    'schema': {
        'oddity': {
            'isodd': True, 
            'type': 'integer'
          }
    }

Custom Data Types
-----------------
You can also add new data types by simply adding ``_validate_type_<typename>``
methods to your subclass. Consider the following snippet from the Eve source
code.

.. code-block:: python

    def _validate_type_objectid(self, field, value):
        """ Enables validation for `objectid` schema attribute.

        :param unique: Boolean, whether the field value should be
                       unique or not.
        :param field: field name.
        :param value: field value.
        """
        if not re.match('[a-f0-9]{24}', value):
            self._error(field, ERROR_BAD_TYPE % 'ObjectId')

This method matches the ``ObjectId`` type in your schema,
allowing something like this:

.. code-block:: python

    'schema': {
        'owner': {
            'type': 'objectid',
            'required': True,
        },
    }

You can also check the `source code`_ for Eve custom validation, where you will
find more advanced use cases, such as the implementation of the ``unique`` and
``data_relation`` constraints. Please see the Cerberus_ documentation for
a complete list rules and data types available. 

.. _unknown:

Allowing the Unknown
--------------------

.. admonition:: MONGODB ONLY FEATURE

    Please note that this is a MongoDB-only feature.

Normally you don't want clients to inject unknown fields in your documents.
However, there might be circumstances where this is desirable. During the
development cycle, for example, or when you are dealing with very heterogeneous
data. After all, not forcing normalized information is one of the selling
points of MongoDB and many other NoSQL data stores.

In Eve, you achieve this by setting the ``ALLOW_UNKNOWN`` option to ``True``.
Once this option is enabled, fields matching the schema will be validated
normally, while unknown fields will be quietly stored without a glitch. You
can also enable this feature only for certain endpoints by setting the
``allow_unknown`` local option.

Consider the following domain:

.. code-block:: python

    DOMAIN: {
        'people': {
            'allow_unknown': True,
            'schema': {
                'firstname': {'type': 'string'},
                }
            }
        }

You normally could only add (POST) or edit (PATCH) `firstnames` to the
``/people`` endpoint. However, since ``allow_unknown`` has been enabled, even
a payload like this will be accepted:

.. code-block:: console

    $ curl -d '[{"firstname": "bill", "lastname": "clinton"}, {"firstname": "bill", "age":70}]' -H 'Content-Type: application/json' http://eve-demo.herokuapp.com/people
    HTTP/1.1 201 OK

.. admonition:: Please note

    Use this feature with extreme caution. Also be aware that, when this
    option is enabled, clients will be capable of actually `adding` fields via
    PATCH (edit).

.. _Cerberus: http://cerberus.readthedocs.org
.. _`source code`: https://github.com/nicolaiarocci/eve/blob/develop/eve/io/mongo/validation.py
