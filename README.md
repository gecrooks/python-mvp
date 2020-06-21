# python-mvp: Minimal Viable Product for an open source, github hosted, python package


[![Build Status](https://travis-ci.org/gecrooks/python-mvp.svg?branch=master)](https://travis-ci.org/gecrooks/python-mvp) [![Documentation Status](https://readthedocs.org/projects/python-mvp/badge/?version=latest)](https://python-mvp.readthedocs.io/en/latest/?badge=latest)

[Source](https://github.com/gecrooks/python-mvp)


## Installation for development

```
$ git clone https://github.com/gecrooks/python-mvp.git
$ cd python-mvp
$ pip install -e .[dev]
```


## About: On the creation and crafting of a python project

This is a minimal template for an open source, github hosted, python package.

## Naming

The first decision to make is the name of the project. And for python packages the most important criteria is that the name isn't already taken on pypi, the repository from which we install python packages with `pip`. So we should do a quick Internet search: This name is available on pypi, there are no other repos of that name on github, and a google search doesn't pull up anything relevant. So we're good to go. 

## License

The next decision is which of the plethora of [Open Source](https://opensource.org/licenses) licenses to use. We'll use the [Apache License](https://opensource.org/licenses/Apache-2.0), a perfectly reasonable choice, and increasingly popular choice. 


## Create github repo

Next we need to initialize a git repo. It's easiest to create the repo on github and clone to our local machine (This way we don't have to mess around setting the origin and such like). Github will helpfully add a `README.md`, the license, and a python `.gitignore` for us. (I'll add `.DS_Store` to the ignore file latter. This is a hidden directory that Mac OS adds all over the place.)


## Clone repo 

On our local machine the first thing we do is create a new conda environment. (You have conda installed, right?) This way if we balls up the installation of some dependency (which happens distressingly often) we can nuke the environment and start again. 
```
    $ conda create --name MVP
    $ source activate MVP
    (MVP) $ python --version
    Python 3.8.3
```

Now we clone the repo locally.
```
    (QC) $ git clone https://github.com/gecrooks/python-mvp.git
    Cloning into 'python-mvp'...
    remote: Enumerating objects: 4, done.
    remote: Counting objects: 100% (4/4), done.
    remote: Compressing objects: 100% (3/3), done.
    remote: Total 4 (delta 0), reused 0 (delta 0), pack-reused 0
    Unpacking objects: 100% (4/4), done.
    (QC) $ cd python-mvp    
```

Lets tag this initial commit for posterities sake (And so I can [link](https://github.com/gecrooks/python-mvp/releases/tag/v0.0.0) to the code at this instance).
```
  (QC) $ git tag v0.0.0
  (QC) $ git push origin v0.0.0
```
For reasons that are unclear to me the regular `git push` doesn't push tags. We have push the tags explicitly by name. Note we need to specify a full MAJOR.MINOR.PATCH version number, and not just e.g. '0.1', for technical reasons that have to do with how we're going to manage package versions.


## Branch
It's always best to craft code in a branch, and then merge that code into the master branch.
```
$ git branch gec001-init
$ git checkout gec001-init
Switched to branch 'gec001-init'
```
I tend to name branches with my initials (so I know it's my branch on multi-developer projects), a serial number (so I can keep track of the chronological order of branches), and a keyword (if I know ahead of time what the branch is for).


## Packaging

Let's complete the minimum viable python project. We need the actual python module, signaled by a (currently) blank `__init__.py` file. 
```
    (QC) $ mkdir python_mvp
    (QC) $ touch python_mvp/__init__.py
```

Python standards for packaging and distribution seems to be in flux (again...). So following what I think the current standard is we need 3 files, `setup.py`, `pyproject.toml`, and `setup.cfg`. 

The modern `setup.py` is just a husk:

```
#!/usr/bin/env python

import setuptools

if __name__ == "__main__":
    setuptools.setup(use_scm_version=True)
```
The only addition is `use_scm_version=True`, which activates versioning with git tags. More on that anon. Don't forget to set executable permissions on the setup.py script.
```
 $ chmod a+x setup.py
```
The [pyproject.toml](https://snarky.ca/what-the-heck-is-pyproject-toml/) file (written in [toml](https://github.com/toml-lang/toml) format) is a recent addition to the canon. It specifies the tools used to build the project.
```
# pyproject.toml
[build-system]
requires = ["setuptools>=42", "wheel", "setuptools_scm[toml]>=3.4"]
build-backend = "setuptools.build_meta"


# pyproject.toml
[tool.setuptools_scm]

```
Again, the parts with `setuptools_scm` are additions. 


All of the rest of the metadata goes in `setup.cfg` (in INI format).
```
# Setup Configuration File
# https://docs.python.org/3/distutils/configfile.html
# [INI](https://docs.python.org/3/install/index.html#inst-config-syntax) file format.

[metadata]
name = python_mvp
url = https://github.com/gecrooks/python_mvp/
author = Gavin Crooks
author_email = gavincrooks@gmail.com
description = "Minimal Viable Product for an open source, github hosted, python package"
long_description= file:README.md
license_file = LICENSE
license = Apache-2.0

classifiers=
    Development Status :: 4 - Beta
    Intended Audience :: Science/Research
    License :: OSI Approved :: Apache Software License
    Topic :: Scientific/Engineering
    Programming Language :: Python
    Natural Language :: English
    Topic :: Software Development :: Libraries
    Topic :: Software Development :: Libraries :: Python Modules
    Programming Language :: Python :: 3
    Programming Language :: Python :: 3.7    
    Programming Language :: Python :: 3.8
    Operating System :: OS Independent

 
[options]
zip_safe = True
python_requires = >= 3.7

# importlib_metadata required for python 3.7
install_requires =
    importlib_metadata
    numpy

setup_requires =
  setuptools_scm

[options.extras_require]
dev =
    pytest
    pytest-cov
	flake8
	mypy
	sphinx
	sphinxcontrib-bibtex
	twine
    setupext-janitor
    setuptools_scm    
```

It's good practice to support at least two consecutive versions of python. But for new projects it's not unreasonable to support only the latest stable python version. 

We can now install our package (as editable -e, so that the code in our repo is live).
```
   $ pip install -e .[dev] 
```   
The optional `[dev]` will install all of the extra packages we need for test and development, listed under `[options.extras_require]` above.



## Versioning
Our project needs a version number (e.g. '3.1.4'). We'll try and follow the [semantic versioning](https://semver.org/) conventions. So as long as the major version number is '0' we're allowed to break things.

There should be a 
[single source of truth](https://packaging.python.org/guides/single-sourcing-package-version/) for this number.
My favored approach is use git tags as the source of truth (Option 7 in the above linked list).  We're going to tag releases anyways, so if we also hard code the version number into the python code we'd violate the single source of truth principle. We use the [setuptools_scm](https://github.com/pypa/setuptools_scm) package to automatically construct a version number from the latest git tag during installation. 

The convention is that the version number of a python packages should be available as `packagename.__version__`. 
So we add the following code to `python_mvp/config.py` to extract the version number metadata.
```
try:
    # python >= 3.8
    from importlib.metadata import PackageNotFoundError, requires, version      # type: ignore
except ImportError:                                                             # pragma: no cover
    # python == 3.7
    from importlib_metadata import PackageNotFoundError, requires, version      # type: ignore

package_name = 'python_mvp'

try:
    __version__ = version(package_name)
except PackageNotFoundError:    # pragma: no cover
    # package is not installed
    __version__ = '?.?.?'


```
and then in `python_mvp/__init__.py`, we import this version number.
```
from .config import __version__ as __version__                      # noqa: F401
```
We put the code to extract the version number in `config.py` and not `__init__.py`, because we don't want to pollute our top level package namespace. 


## about

One of my tricks is to add a main function in `python_mvp/about.py` to print the versions of the core upstream dependencies. This can be extremely helpful when debugging configuration or system dependent bugs, particularly when running continuous integration tests. 

```
# Configuration (> python -m python_mvp.about)
platform                 macOS-10.13.6-x86_64-i386-64bit
python_mvp               0.0.1.dev0+g530b742.d20200621
python                   3.8.3
numpy                    1.18.5
pytest                   5.4.3
pytest-cov               2.10.0
flake8                   3.8.3
mypy                     0.780
sphinx                   3.1.1
sphinxcontrib-bibtex     1.0.0
twine                    3.1.1
setupext-janitor         1.1.2
setuptools_scm           4.1.2
```
The `about()` function to print this information is placed in `config.py`. The file `about.py` contains the standard python command line interface (CLI), 
```
if __name__ == '__main__':
    import python_mvp
    python_mvp.about()
```
It's important that `about.py` isn't imported by any other code in the package, else we'll get multiple import warnings when we try to run the CLI. 


## Unit tests

Way back when I worked as a commercial programmer, the two most important things I learned were source control, and unit tests. Both were largely unknown in the academic world at the time. 

(I was once talking to a chap who was developing a new experimental platform. The plan was to build several dozens of these things, and sell them to other research groups so they didn't have to build their own. A couple of grad students wandered in. They were working with one of the prototypes, and they'd found some minor bug. Oh yes, says the chap, who goes over to his computer, pulls up the relevant file, edits the code, and gives the students a new version of that file. He didn't run any tests. There were no unit tests. And there was no source control, so there was no record of the change he'd just made. That was it. The horror.)

The two main options for unit tests appear to be `unittest` from the standard libraray and `pytest`. To me `unittest` feels very javonic. There's a lot of boiler plate code and I believe it's a direct descendant of an early java unit testing framework. Pytest, on the other hand, feels more pythonic. In the basic case all we have to do is to write functions (whose names are prefixed with 'test_'), within which we test code with `asserts`. Easy.

There's two common ways to organize tests. Either we place tests in a separate directory, or they live in the main package along with the rest of the code. In the past I've used the former approach. It keeps the test organized and separate from the production code. But I'm going to try the second approach for this project. The big advantage is that the unit tests for a piece of code live right next to the code being tested.

Let's test that we can access the version number (There is no piece of code too trivial that it shouldn't have a unit test.) In `python_mvp/config_test.py` we add

```
import python_mvp

def test_version():
    assert python_mvp.__version__
```
and run our test. (The 'python -m' prefix isn't strictly necessary, but it helps ensure that pytest is running under the correct copy of python.)
```

(QC) $ python -m pytest
========================================================================================== test session starts ===========================================================================================
platform darwin -- Python 3.8.3, pytest-5.4.3, py-1.8.2, pluggy-0.13.1
rootdir: /Users/work/Work/Projects/python_mvp
collected 1 item                                                                                                                                                                                         

python_mvp/config_test.py .                                                                                                                                                                            [100%]

=========================================================================================== 1 passed in 0.02s ============================================================================================
```

Note that in the main code we'll access the package with relative imports, e.g.
```
from . import __version__
```
But in the test code we use absolute imports. 
```
from python_mvp import __version__
```
In tests we want to access our code in the same way we would access it from the outside as an end user.


## Test coverage

We want to monitor the test coverage. At a bare minimum the unit tests should run all the code. The [pytest-cov](https://pypi.org/project/pytest-cov/) plugin to pytest will do this for us. Configuration is placed in the setup.cfg file (Config can also be placed in a seperate `.coveragerc`, but I think its better to avoid a proliferation of configuration files.)
```
# pytest configuration
[tool:pytest]
testpaths =
    python_mvp


# Configuration for test coverage
#
# https://coverage.readthedocs.io/en/latest/config.html
#
# python -m pytest --cov

[coverage:paths]
source =
    python_mvp

[coverage:run]
omit =
    *_test.py

[coverage:report]
# Use ``# pragma: no cover`` to exclude specific lines
exclude_lines =
    pragma: no cover
```

We have to explicitly omit the unit tests since we have placed the test files in the same directories as the code to test.


## Linting

We need to lint our code before pushing any commits. I like [flake8](https://flake8.pycqa.org/en/latest/). It's faster than pylint, and I think better error messages. I will hereby declare:

    The depth of the indentation shall be 4 spaces. 
    And 4 spaces shall be the depth of the indentation. 
    Two spaces thou shall not use. 
    And tabs are right out. 

Four spaces is standard. [Tabs are evil](https://www.emacswiki.org/emacs/TabsAreEvil). I've worked on a project with 2-space indents, and I see the appeal, but I found it really weird (Although maybe that's just lack of exposure.)

Most of flake8's defaults are perfectly reasonable and in line with [PEP8](https://www.python.org/dev/peps/pep-0008/) guidance. But even [Linus](https://lkml.org/lkml/2020/5/29/1038) agrees that the old standard of 80 columns of text is too restrictive. The configuration also lives in `setup.cfg`.
```
# flake8 linter configuration
[flake8]
max-line-length = 99
```
We need to override the linter on occasion. We add pragma such as `# noqa: F401` to assert that no, really, in this case we do know what we're doing.


## Copyright
It's common practice to add a copyright and license notice to the top of every source file -- something like this:
```

# Copyright 2019-, Gavin E. Crooks and contributors
#
# This source code is licensed under the Apache License, Version 2.0 found in
# the LICENSE.txt file in the root directory of this source tree.

```

I tend to forget to add these lines. So let's add a unit test `python_mvp/config_test.py::test_copyright` to make sure we don't.
```
def test_copyright():
    """Check that source code files contain a copyright line"""
    exclude = set(['python_mvp/version.py'])
    for fname in glob.glob('python_mvp/**/*.py', recursive=True):
        if fname in exclude:
            continue
        print("Checking " + fname + " for copyright header")

        with open(fname) as f:
            for line in f.readlines():
                if not line.strip():
                    continue
                assert line.startswith('# Copyright')
                break
```


## API Documentation
[Sphinx](https://www.sphinx-doc.org/en/master/usage/quickstart.html) is the standard 
tool used to generate API documentation from the python source. Use the handy quick start tools. 
```
$ mkdir docs
$ cd docs
$ sphinx-quickstart
```
The defaults are reasonable. Enter the project name and author when prompted. 

Edit the conf.py, and add the following collection of extensions.
```
extensions = [
    'sphinx.ext.autodoc',
    'sphinx.ext.napoleon',
]
```
[Autodoc](https://www.sphinx-doc.org/en/master/usage/extensions/autodoc.html) automatically extracts documentation form docstrings, and [napolean](https://sphinxcontrib-napoleon.readthedocs.io/en/latest/example_google.html) enables [Google style](http://google.github.io/styleguide/pyguide.html) python docstrings.

We also add a newline at the end of `conf.py`, since the lack of a blank line at the end upsets our linter.

Go ahead and give it a whirl. This won't do anything much interesting yet, but it's a start.
```
$ make html
```

One problem is that sphinx creates three (initially) empty directories, `_build`, `_static`, and `_templates`. But we can't add empty directories to git. Git only tracks files. The trick is to add an empty `.gitignore` files to each of these directories. But we don't want to add any other files from `_build`, so that directory needs to be explicitly listed in the master `.gitignore` (Which github has already done for us, so we have to add the '-f' force option to git add).

```
$ touch _templates/.gitignore _build/.gitignore _static/.gitignore
$ git add -f _templates/.gitignore _build/.gitignore _static/.gitignore
$ git add Makefile *.*
# cd ..
```



## Makefile
I like to add a Makefile with targets for all of the common development tools I need to run. This is partially for convenience, and partially as documentation, i.e. here are all the commands you need to run to test, lint, typecheck and build the code (and so on.) I use a [cleaver hack](https://marmelab.com/blog/2016/02/29/auto-documented-makefile.html) so that the makefile self documents. 

```
(QC) $ make
all          Run all tests
test         Run unittests
coverage     Report test coverage using current backend
lint         Delint python source
typecheck    Static typechecking 
docs         Build documentation
docs-open    Build documentation and open in webbrowser
docs-clean   Clean documentation build
pragmas      Report all pragmas in code
about        Report versions of dependent packages
status       git status -uno
build        Setuptools build
clean        Clean up after setuptools
```

The pragmas target searches the code and lists all of the pragmas that occur. Common uses of [pragmas](https://en.wikipedia.org/wiki/Directive_(programming)) are to override the linter, or tester, or typechecker. I also tend to scatter other keywords throughout my code: TODO (For things that need doing), FIXME (For code that's broken, but I can't fix right this moment), DOCME (code that needs more documentation, and TESTME (for code that needs more tests). In principle, production code shouldn't have these pragmas. Either the problem should be fixed, or if it can't be immediately fixed, it should become a github issue. 


## Readthedocs
We'll host our API documentation on [Read the Docs](readthedocs.org). We'll need a basic configuration file, `.readthedocs.yml`.
```
version: 2
formats: []
sphinx:
  configuration: docs/conf.py
python:
  version: 3.8
```
I've already got a readthedocs account, so setting up a new project takes but a few minutes. 


## README.md

We add some basic information and installation instructions to `README.mb`. Github displays this file on your project home page (but under the file list, so if you have a lot of files at the top level of your project, people might not notice your README.)

A handy trick is to add Build Status and Documentation Status badges for travis and readthedocs. These will proudly declare that your tests are passing (hopefully).  (See top of this file)


## Continuous Integration

Another brilliant advance to software engineering practice is continuous integration (CI). The basic idea is that all code gets thoroughly tested before it's added to the master branch.

I use [travis-ci](https://travis-ci.org/). I already have travis setup and hooked into my github, so it only takes a few clicks to setup a new project. We need a `.travis.yml` configuration file (in [yaml](https://yaml.org/) format).
```
calanguage: python
sudo: required
dist: xenial

python:
  - "3.8"

before_install:
  - pip install -U pip

install:
  - pip install .[dev]  # install package + test dependencies

script:
  - python -m python_mvp.about
  - python -m pytest --cov=python_mvp --cov-fail-under 100
  - flake8
  - mypy
  - sphinx-build -M html docs docs/_build
```
Note that these tests are picky. Not only must the unit tests pass, but test coverage must be 100%, the code must delinted and properly typed, and the docs have to build without error.  

Let's add, commit, and push our changes. 
```
$ git status
On branch gec001-init
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	new file:   .readthedocs.yml
	new file:   .travis.yml
	new file:   Makefile
	modified:   README.md
	new file:   docs/Makefile
	new file:   docs/_build/.gitignore
	new file:   docs/_static/.gitignore
	new file:   docs/_templates/.gitignore
	new file:   docs/conf.py
	new file:   docs/index.rst
	new file:   pyproject.toml
	new file:   python_mvp/__init__.py
	new file:   python_mvp/about.py
    new file:   python_mvp/config.py
	new file:   python_mvp/config_test.py
	new file:   setup.cfg
	new file:   setup.py
    
$ git commit -m "Minimum viable package"
...
$ git push --set-upstream origin gec001-init
...
```
If all goes well travis will see our push to github, and build and test the code in the branch. (You may have to make a pull request for travis to see this first test attempt) Probably all the tests won't pass on the first try. It's easy to forget something (which is why we have automatic tests). So tweak the code, and push another commit until the tests pass.



## PyPi

We should now be ready to do a test submission to PyPI, The Python Package Index (PyPI).
Follow the directions laid out in the [python packaging](https://packaging.python.org/tutorials/packaging-projects/) documentation.

```
$ pip install -q twine
...
$ git tag v0.1.0rc1
$ python setup.py sdist bdist_wheel 
...
```
We tag our release candidate so that we get a clean version number (pypi will object to the development version numbers setuptools_scm generates if the tag or git repo isn't up to date).

First we push to the pypi's test repository.
```
(QC) $ python -m twine upload --repository testpypi dist/*
```
You'll need to create a pypi account if you don't already have one. 

Let's make sure it worked by installing from pypi into a fresh conda environment.
```
(QC) $ conda deactivate
$ conda create --name tmp
$ conda activate tmp
(tmp) $ pip install --index-url https://test.pypi.org/simple/ --no-deps python_mvp
(tmp) $ python -m python_mvp.about
(tmp) $ conda activate QC
```

It's a good idea to install Dave Shawley's [setupext-janitor](https://github.com/dave-shawley/setupext-janitor).
```
(QC) $ pip install -q setupext-janitor
```
Setuptools has a clean command to remove build files, but it doesn't actually do a good job of cleaning up after itself. But with `setupext-janitor` installed we can remove all of the files that the build process generates.
```
(QC) $ ./setup.py clean --all
```

## Merge and Tag

Over on github we create a pull request, wait for travis to give us the green light once all the tests have passed, and then squash and merge. 

The full developer sequence goes something like this

1.) Sync the master branch. 
```
$ git checkout master
$ git pull origin master
```
(If we're working on somebody else's project, this step is a little more complicated. We fork the project on github, clone our fork to the local machine, and then set git's 'upstream' to be the original repo. We then sync out local master branch with the upstream master branch
```
$ git checkout master
$ git fetch upstream
$ git merge upstream/master
```
This should go smoothly as long as you never commit directly to your local master branch.)


2.) Create a working branch.
```
$ git branch BRANCH
$ git checkout BRANCH
```

3.) Do a bunch of development on the branch, committing incremental changes as we go along.

4.) Sync the master branch with github (since other development may be ongoing.) (i.e. repeat step 1)

5.) Rebase our branch to master. 
```
$ git checkout BRANCH
$ git rebase master
```
If there are conflicts, resolve them, and then go back to step 4.

6.) Sync our branch to github

```
$ git push
```

7.) Over on github, create a pull request to merge into the master branch

8.) Wait for the integration tests to pass. If they don't, fix them, and then go back to step 1.

9.) Squash and merge into the master branch on github. Squashing merges all of our commits on the branch into a single commit to merge into the master branch. We generally don't want to pollute the master repo history with lots of micro commits. (On multi-developer projects, code should be reviewed. Somebody other than the branch author approves the changes before the final merge into master. )

10.) Goto step 1. Back on our local machine, we resync master, create a new branch, and continue developing. 


## Tag and release

Assuming everything went well, you can now upload a release to pypi proper. Sync our local master branch, as in step 1 above, tag, and upload to pypi

```
$ git checkout master
$ git tag v0.1.0
$ git push origin v0.1.0
$ python setup.py clean --all
$ python setup.py sdist bdist_wheel
$ python -m twine upload dist/*
```


## Conclusion

By my count we have 13 configuration files (In python, toml, yaml, INI, gitignore, and Makefile formats), 3 documentation files (including the license), one file of unit tests, and 3 (short) files of code (With 31 lines of code). We're now ready to create a new git branch and start coding in earnest.


