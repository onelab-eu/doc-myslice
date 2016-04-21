Welcome to doc-myslice's documentation!
=======================================

Contents:

.. toctree::
   :maxdepth: 2

Architecture
==================


Setup
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`

Configuration
==================

REST API
==================

http://localhost:8111/api/v1/activity

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
        "type": "KEY",
        "key": "xxxxxxx"
	        }
    }
}