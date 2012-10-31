asyncmongo3
===========

AsyncMongo is an asynchronous library for accessing mongo 
which is built on the tornado ioloop.

This fork is an experimental attempt to make it work in Python 3.
Initially it will be a quick and dirty fix, with no attempt to
ensure it still works under Python 2.

Installation
------------

Installing from github: `pip-3.2 install  git+https://github.com/Tsumanga-Studios/asyncmongo3.git`
(or whatever your Python 3 pip is called)

Installing from source: `git clone git://github.com/Tsumanga-Studios/asyncmongo3.git; cd asyncmongo; python3 setup.py install`

Usage
-----

asyncmongo syntax strives to be similar to [pymongo](http://api.mongodb.org/python/current/api/pymongo/collection.html).

    import asyncmongo
    import tornado.web
    
    class Handler(tornado.web.RequestHandler):
        @property
        def db(self):
            if not hasattr(self, '_db'):
                self._db = asyncmongo.Client(pool_id='mydb', host='127.0.0.1', port=27017, maxcached=10, maxconnections=50, dbname='test')
            return self._db
    
        @tornado.web.asynchronous
        def get(self):
            self.db.users.find({'username': self.current_user}, limit=1, callback=self._on_response)
            # or
            # conn = self.db.connection(collectionname="...", dbname="...")
            # conn.find(..., callback=self._on_response)
    
        def _on_response(self, response, error):
            if error:
                raise tornado.web.HTTPError(500)
            self.render('template', full_name=response['full_name'])

About
-----

As of latest merge from upstream: some features are not currently implemented: 

* directly interfacing with indexes, dropping collections
* retrieving results in batches instead of all at once 
(asyncmongo's nature means that no calls are blocking regardless of the number of results you are retrieving)
* native Replica Set support #16
* tailable cursors #15

It is also likely to be broken in various ways due to subtle differences between Python 2 and Python 3 semantics.


Requirements
------------
The following two python libraries are required.

* [pymongo](http://github.com/mongodb/mongo-python-driver) version 1.9+ for bson library
* [tornado](http://github.com/facebook/tornado)

Versions of them exist for Python 3 - use those ones.

Issues
------

Please report any issues via [github issues](https://github.com/Tsumanga-Studios/asyncmongo3/issues)

