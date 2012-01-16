Heroku buildpack: Python
========================

This is a [Heroku buildpack](http://devcenter.heroku.com/articles/buildpack) for Python apps.
It uses [virtualenv](http://www.virtualenv.org/) and [pip](http://www.pip-installer.org/).

It has been modified by Bolster Labs, Inc. to address specific needs.

Modifications include:

 * Creation of a BUILD_ID; injected into Django settings and also written into `build.id`
 * Running of collectstatic in the slug compliation process
 * Installation of Python eggs present in the vendor directory and listed in
   `vendor/requirements.dist.txt`

Usage
-----

Example usage:

    $ ls
    Procfile  requirements.txt  web.py

    $ heroku create --stack cedar --buildpack git@github.com:bolster/heroku-buildpack-bolster.git

    $ git push heroku master
    ...
    -----> Heroku receiving push
    -----> Fetching custom build pack... done
    -----> Python app detected
    -----> Preparing virtualenv version 1.6.4
           New python executable in ./bin/python
           Installing setuptools............done.
           Installing pip...............done.
    -----> Installing dependencies using pip version 1.0.2
           Downloading/unpacking Flask==0.7.2 (from -r requirements.txt (line 1))
           Downloading/unpacking Werkzeug>=0.6.1 (from Flask==0.7.2->-r requirements.txt (line 1))
           Downloading/unpacking Jinja2>=2.4 (from Flask==0.7.2->-r requirements.txt (line 1))
           Installing collected packages: Flask, Werkzeug, Jinja2
           Successfully installed Flask Werkzeug Jinja2
           Cleaning up...

The buildpack will detect your app as Python if it has the file `requirements.txt` in the root. It will detect your app as Python/Django if there is an additional `settings.py` in a project subdirectory.

It will use virtualenv and pip to install your dependencies, vendoring a copy of the Python runtime into your slug.  The `bin/`, `include/` and `lib/` directories will be cached between builds to allow for faster pip install time.

It will use easy_install to install eggs listed in `vendor/requirements.dist.txt`. The format of this file is `package_name:package_hash:path_from_root`. Only the value of `path_from_root` is used by the buildpack. The other values are used by proprietary build tools; we are investigating the possibility of releasing these tools, but the process can easily be performed manually.

Hacking
-------

To use this buildpack, fork it on Github.  Push up changes to your fork, then create a test app with `--buildpack <your-github-url>` and push to it.

To change the vendored virtualenv, unpack the desired version to the `src/` folder, and update the virtualenv() function in `bin/compile` to prepend the virtualenv module directory to the path. The virtualenv release vendors its own versions of pip and setuptools.
