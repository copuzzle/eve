.. meta::
   :description: Eve is an open source Python REST API framework designed for human beings.

REST API Framework for Humans
=============================
Eve is an :doc:`open source <license>` Python REST API framework designed for human beings.

Eve is powered by Flask_ and supports both MongoDB_ and SQL_ backends. It
allows to effortlessly build and deploy highly customizable, fully featured
RESTful Web Services. 

The codebase is thoroughly tested under Python 2.6, 2.7, 3.3, 3.4 and PyPy.

Eve is Simple
-------------
.. code-block:: python

    from eve import Eve

    app = Eve()
    app.run()

Your API is now live and ready to be consumed like so:

.. code-block:: console

    $ curl -i http://example.com/people
    HTTP/1.1 200 OK

All you need to bring your API online is a database, a configuration file and
a launch script.  Overall, you will find that configuring and fine-tuning your
API is a very simple process.

.. _demo:

Live demo
---------
Check out the `live demo`_. If using a browser you will get XML back. For JSON
in the browser, you might want to install Postman_ or similar extension and
then set the ``Accept`` request header to ``application/json``. If you are
a CLI user (and you should), ``curl`` is your friend. The `source code`_ will
show you how easy it is to run an API with Eve. You will also find `usage
examples`_ for all common use cases (GET, POST, PATCH, DELETE and more). There
is also a simple `client app`_ available.

Development Version
--------------------
If you are on python-eve.org_ then you are looking at the documentation of the
development version. Looking for last release docs? Follow `this
link <http://eve.readthedocs.org/en/stable/>`_.

User's Guide
------------
.. toctree::
    :maxdepth: 2

    foreword
    rest_api_for_humans
    install
    quickstart
    features
    config
    validation
    authentication
    tutorials/index
    extensions

Developer's Guide
-----------------
.. toctree::
    :maxdepth: 1

    contributing
    testing

*A proper developer guide will be available when 1.0 is released*

Support, Updates and Licensing
------------------------------
.. toctree::
    :maxdepth: 1

    support
    updates
    authors
    license

Changelog
---------
.. toctree::
    :maxdepth: 2

    changelog

.. note::
   This documentation is under development. Please refer to the links on the
   sidebar for more information, or to get in touch with the development team
   (that being me_).

.. _python-eve.org: http://python-eve.org
.. _`Eve Demo instructions`: http://github.com/nicolaiarocci/eve-demo#readme
.. _`live demo`: http://eve-demo.herokuapp.com
.. _`source code`: https://github.com/nicolaiarocci/eve-demo
.. _`usage examples`: https://github.com/nicolaiarocci/eve-demo#readme
.. _`client app`: https://github.com/nicolaiarocci/eve-demo-client
.. _me: mailto:me@nicolaiaroccicom
.. _Postman: https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&ved=0CC0QFjAA&url=https%3A%2F%2Fchrome.google.com%2Fwebstore%2Fdetail%2Fpostman-rest-client%2Ffdmmgilgnpjigdojojpjoooidkmcomcm&ei=dPQ7UpqEBISXtAbPpIGwDg&usg=AFQjCNFL71vN61QG0LKlw7VDJvIZDprjHA&bvm=bv.52434380,d.Yms
.. _Flask: http://flask.pocoo.org/
.. _SQL: http://www.sqlalchemy.org/
.. _MongoDB: https://mongodb.org
