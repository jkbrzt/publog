====================================================================
Setting up Python development environment on OS X 10.8 Mountain Lion
====================================================================

The Python version `Mountain Lion`_ ships with is `2.7.2`_ despite the fact
that `Python 2.7.3`_ has been available for a while and includes several
security fixes. Moreover, the Apple-provided Python doesn't include source code
for standard library modules (only compiled ``.pyc`` and ``.pyo`` files).
This makes it a poor fit for Python development.

This post describes the steps the author has taken in order to install an
isolated Python 2.7.3 via `Homebrew`_ without modifying the system installation
and also how to install and configure `virtualenv`_ and `virtualenvwrapper`_.
Please feel free to open an issue or send a pull request if something doesn't
work as advertised.


Prerequisites: XCode and Homebrew
---------------------------------

XCode
^^^^^

Install `XCode 4.4`_ from the Mac App Store and make sure to install also
its Command Line Tools from ``XCode > Preferences… > Downloads``:

.. image:: https://github.com/jkbr/Publog/raw/master/2012-07-26-Python-vs-Mountain-Lion/install-command-line-tools.png
  :target: http://travis-ci.org/jkbr/httpie
  :alt: Installing Command Line Tools from XCode Preferences

That will give us a Homebrew-friendly environment.


Homebrew
^^^^^^^^

Follow `installation instructions for Homebrew`_ for and don't forget to
prepend ``/usr/local/bin`` and ``/usr/local/sbin`` to your ``$PATH``::

        echo 'export PATH="/usr/local/bin:/usr/local/sbin:$PATH"' >> ~/.bash_profile

That will ensure that Homebrew-installed binaries, including the Python one,
will have precedence over the default ones in ``/usr/bin`` and elsewhere.


Installing Python
-----------------

At this point, ``python`` still points to the Apple-provided Python
installation::

    $ which python
    /usr/bin/python

    $ python
    >>> import sys
    >>> sys.version
    '2.7.2 (default, Jun 20 2012, 16:23:33) \n[GCC 4.2.1 Compatible Apple Clang 4.0 (tags/Apple/clang-418.0.60)]'
    >>> sys.prefix
    '/System/Library/Frameworks/Python.framework/Versions/2.7'

The Python `formula`_ seems to be currently broken and unless this
`pull request`_ has already been merged ``brew install python`` will most
likely fail. To work around it we need to point Homebrew directly to the
fixed formula::

        brew install https://raw.github.com/samueljohn/homebrew/52289be4b29665851986b5b87242dba680b2618a/Library/Formula/python.rb

This installs Python into ``/usr/local/Cellar/python/2.7.3``. You might need to
run ``brew link python`` afterwards.

Open a new terminal window and verify the installation with::

    $ which python
    /usr/local/bin/python

    $ python
    >>> import sys
    >>> sys.version
    '2.7.3 (default, Jul 26 2012, 15:37:27) \n[GCC 4.2.1 Compatible Apple Clang 4.0 ((tags/Apple/clang-421.0.57))]'
    >>> sys.prefix
    '/usr/local/Cellar/python/2.7.3/Frameworks/Python.framework/Versions/2.7'

You will also need to add ``/usr/local/share/python`` to your ``$PATH`` which
is where ``pip`` will place scripts included in packages it installs::

    echo 'export PATH="/usr/local/share/python:${PATH}"' >> ~/.bash_profile

The ``site-packages`` directory is ``/usr/local/lib/python2.7/site-packages``
and ``pip`` doesn't require ``sudo`` to install packages to it.


Installing Virtualenv and Virtualenvwrapper
-------------------------------------------

Install ``virtualenv`` and ``virtualenvwrapper`` into the global site-packages
directory via ``pip``::

    pip install virtualenv virtualenvwrapper

Configure ``virtualenvwrapper`` to get the handy shortcuts it provides such as
``mkvirtualenv``::

    # Home for Python virtual environments created with mkvirtualenv:
    mkdir ~/.virtualenvs
    echo 'export WORKON_HOME=~/.virtualenvs' >> ~/.bash_profile

    echo 'source /usr/local/share/python/virtualenvwrapper.sh' >> ~/.bash_profile

Open a new terminal window or run ``source ~/.bash_profile`` and you should be
good to go::

    $ mkvirtualenv my-project
    New python executable in my-project/bin/python
    Installing distribute...done.
    Installing pip...done.
    virtualenvwrapper.user_scripts creating ~/.virtualenvs/my-project/bin/predeactivate
    virtualenvwrapper.user_scripts creating ~/.virtualenvs/my-project/bin/postdeactivate
    virtualenvwrapper.user_scripts creating ~/.virtualenvs/my-project/bin/preactivate
    virtualenvwrapper.user_scripts creating ~/.virtualenvs/my-project/bin/postactivate
    virtualenvwrapper.user_scripts creating ~/.virtualenvs/my-project/bin/get_env_details

    (my-project)$ which pip
    ~/.virtualenvs/my-project/bin/pip

    (my-project)$ pip install Django
    Downloading/unpacking django
      Downloading Django-1.4.tar.gz (7.6Mb): 7.6Mb downloaded
      Running setup.py egg_info for package django

    Installing collected packages: django
      Running setup.py install for django
        changing mode of build/scripts-2.7/django-admin.py from 644 to 755

        changing mode of ~/.virtualenvs/my-project/bin/django-admin.py to 755
    Successfully installed django
    Cleaning up...


.. _Mountain Lion: http://itunes.apple.com/us/app/os-x-mountain-lion/id537386512?ls=1&mt=12
.. _XCode 4.4: http://itunes.apple.com/us/app/xcode/id497799835?mt=12
.. _2.7.2: http://www.python.org/getit/releases/2.7.2/
.. _Python 2.7.3: http://www.python.org/getit/releases/2.7.3/
.. _Homebrew: http://mxcl.github.com/homebrew/
.. _virtualenv: http://www.virtualenv.org/en/latest/index.html
.. _virtualenvwrapper: http://www.doughellmann.com/projects/virtualenvwrapper/
.. _installation instructions for Homebrew: https://github.com/mxcl/homebrew/wiki/installation
.. _formula: https://github.com/samueljohn/homebrew/blob/master/Library/Formula/python.rb
.. _pull request: https://github.com/mxcl/homebrew/pull/12968
