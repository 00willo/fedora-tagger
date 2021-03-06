Fedora-Tagger
=============

A Flask app for helping us tag fedora packages.

Hotkeys
-------

.. hotkeys

+--------------------+----------------+
| ``j`` *or* ``↓``   | next item      |
+--------------------+----------------+
| ``k`` *or* ``↑``   | previous item  |
+--------------------+----------------+
| ``l`` *or* ``→``   | upvote tag     |
+--------------------+----------------+
| ``h`` *or* ``←``   | downvote tag   |
+--------------------+----------------+
| ``b``              | leaderboard    |
+--------------------+----------------+
| ``t``              | statistics     |
+--------------------+----------------+
| ``s``              | search packages|
+--------------------+----------------+
| ``esc``            | help           |
+--------------------+----------------+

Requires authentication.

+--------------------+----------------+
| ``i`` *or* ``a``   | add new tag    |
+--------------------+----------------+

.. hotkeys

Hacking on Fedora Tagger
------------------------

You can setup a development environment like this::

    # Create a virtualenv
    $ sudo yum install -y python-virtualenvwrapper
    $ mkvirtualenv tagger

    # Install dependencies
    $ pip install kitchen paver
    $ pip install git+https://github.com/fedora-infra/python-fedora.git
    $ pip install -r requirements.txt

    # Run the tests
    $ pip install -r test-requirements.txt
    $ ./runtests.sh

    # Install the egg link
    $ python setup.py develop

    # Create a dev database
    $ python createdb.py --with-dev-data

    # Run the development server
    $ python runserver.py

And lastly point your browser at http://localhost:5000

The REST API
------------

Visit http://localhost:5000/api/ for some HTML docs.  You can use the
awesome `HTTPie <https://github.com/jkbr/httpie>`_ to try it from the
command line::

    $ sudo yum -y install httpie

You can GET tagger's data on a package::

    $ http get http://localhost:5000/api/nethack/
    HTTP/1.1 200 OK
    Connection: close
    Content-Length: 750
    Content-Type: application/json
    Date: Wed, 10 Apr 2013 01:46:58 GMT
    Server: Apache/2.2.15 (Red Hat)

    {
        "icon": "https://apps.fedoraproject.org/packages/images/icons/package_128x128.png",
        "name": "nethack",
        "rating": -1.0,
        "summary": "",
        "tags": [
            {
                "dislike": 0,
                "like": 5,
                "package": "nethack",
                "tag": "games",
                "total": 5,
                "votes": 5
            },
            {
                "dislike": 0,
                "like": 4,
                "package": "nethack",
                "tag": "rogue-like",
                "total": 4,
                "votes": 4
            },
            {
                "dislike": 0,
                "like": 4,
                "package": "nethack",
                "tag": "the greatest game ever made",
                "total": 4,
                "votes": 4
            },
            {
                "dislike": 0,
                "like": 4,
                "package": "nethack",
                "tag": "@",
                "total": 4,
                "votes": 4
            }
        ]
    }

Or PUT your rating on a package::

    $ http put http://localhost:5000/api/rating/nethack/ pkgname=nethack rating=100
    HTTP/1.1 200 OK
    Connection: close
    Content-Length: 97
    Content-Type: application/json
    Date: Wed, 10 Apr 2013 01:49:07 GMT
    Server: Apache/2.2.15 (Red Hat)

    {
        "messages": [
            "Rating on package \"nethack\" changed to \"100\""
        ],
        "output": "ok"
    }

Or PUT your vote on a particular tag::

    $ http put http://localhost:5000/api/vote/nethack/ pkgname=nethack tag=rogue-like vote=1
    HTTP/1.1 200 OK
    Connection: close
    Content-Length: 353
    Content-Type: application/json
    Date: Wed, 10 Apr 2013 01:53:32 GMT
    Server: Apache/2.2.15 (Red Hat)

    {
        "messages": [
            "Vote added on the tag \"rogue-like\" of the package \"nethack\""
        ],
        "output": "ok",
        "tag": {
            "dislike": 0,
            "like": 5,
            "package": "nethack",
            "tag": "rogue-like",
            "total": 5,
            "votes": 5
        },
        "user": {
            "anonymous": true,
            "rank": -1,
            "username": "anonymous",
            "votes": 8
        }
    }
