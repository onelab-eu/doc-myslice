Welcome to doc-myslice's documentation!
=======================================

Contents:

.. toctree::
   :maxdepth: 2

Architecture
==================

Web - REST API - Websocket
------------
myslice/bin/myslice-web -> Tornado web -> server (myslice/web/__init__.py)

myslice/web/__init__.py -> Web + REST API + Websocket

myslice/web/rest/activity.py -> GET/POST to /api/v1/activity

Event -> myslice/db/activity.py -> RethinkDB

Services
------------
myslice/bin/myslice-server -> runs the serviceActivity process

.. highlight:: python
	p = multiprocessing.Process(target=serviceActivity) 

This service is located into: myslice/services/activity.py

This service observes the changes in RethinkDB 

.. highlight:: python
	feed = changes(table='activity')

When there is an event, add it to the Queue

.. highlight:: python
	qEvents.put(event)

The serviceActivity is processing the Queue in a Thread

.. highlight:: python
	t = threading.Thread(target=manageEvents, args=(qEvents,))

The process triggers the event worker
myslice/services/workers/events.py

It will change the status of the event to pending or waiting
.. highlight:: python
	dispatch(dbconnection, event)
	Update the status of the event into RethinkDB

Other services are running and will take care of the event based on the object.type
For example the serviceUsers 

.. highlight:: python
    p = multiprocessing.Process(target=serviceUsers)

This service is watching for events that are ready and related to users

.. highlight:: python
if event.object.type == ObjectType.USER:
	# event.isReady() = Request APPROVED or Event WAITING
   if event.isReady():
   		qUserEvents.put(event)

Then the event is sent to the Queue triggering the corresponding thread

.. highlight:: python
	t = threading.Thread(target=manageUsersEvents, args=(lock, qUserEvents ))

The Queries are sent to MySlice Lib, which handles then the SFA API


Setup
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`

Configuration
==================


Events
==================
status: NEW | WAITING | RUNNING | SUCCESS | WARNING | ERROR


REST API
==================

http://localhost:8111/api/v1/activity

action: ADD | DEL | MOD | REQ

ADD a property to an object
DEL a property of an object
MOD properties of an object
REQ create an object
??? delete an object

user: has generated the event

object: AUTHORITY | USER | PROJECT | SLICE | RESOURCE

data: properties of the object {} | one property of the object {type, value}

.. highlight:: javascript
{
"event": {
	"action": "ADD",
	"user": "urn:publicid:IDN+onelab:upmc:apitest+user+zhouquantest",
    "object": {
                "type": "USER",
                "id": "urn:publicid:IDN+onelab:upmc:apitest+user+zhouquantest"
                },
	"data": {
        "type": "key",
        "value": "xxxxxxx"
	        }
    }
}