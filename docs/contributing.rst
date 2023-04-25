Contributing
------------

Thank you for your interest in contributing! We welcome all contributions no matter their size.

This document covers contributing to ``<REPO_NAME>`` at a high level. For more information on how we do testing, pull requests, documentation, and more, please checkout the `Snake Charmers Tactical Manual <https://github.com/ethereum/snake-charmers-tactical-manual>`_.

Please read along to learn how to get started.


Setting the stage
~~~~~~~~~~~~~~~~~

First, we need to clone the <PROJECT_NAME> repository:

.. code:: sh

    $ git clone https://github.com/ethereum/<REPO_NAME>.git


.. include:: /fragments/virtualenv_explainer.rst

After we have activated our virtual environment, we need to install all dependencies that are needed to run, develop, and test.
This is as easy as navigating to the ``<REPO_NAME>`` directory and running:

.. code:: sh

    python -m pip install -e ".[dev]"


Running the tests
~~~~~~~~~~~~~~~~~

A great way to explore the code base is to run the tests.

We can run all tests with:

.. code:: sh

    pytest tests

However, you may just want to run a subset instead, like:

.. code:: sh

    pytest tests/path/to/file/test_stuff.py


Code Style
~~~~~~~~~~

When multiple people are working on the same body of code, it is important that they write code that conforms to a similar style. It often doesn't matter as much which style, but rather that they conform to one style.

We use ``black``, ``flake8``, and ``isort`` to enforce consistent style.
You can verify that your contribution conforms by running:

.. code:: sh

    make lint


Type Hints
~~~~~~~~~~

This codebase uses `type hints <https://www.python.org/dev/peps/pep-0484/>`_. Type hints make it easy to prevent certain types of bugs, enable richer tooling, and enhance the documentation, making the code easier to follow.

All new code is required to land with type hints, with the exception of test code that is not expected to use type hints.

All parameters, as well as the return type of functions, are expected to be typed, with the exception of ``self`` and ``cls`` as seen in the following example.

.. code:: python

    def __init__(self, wrapped_db: DatabaseAPI) -> None:
        self.wrapped_db = wrapped_db
        self.reset()


Documentation
~~~~~~~~~~~~~

Good documentation will lead to quicker adoption and happier users. Please check out our guide
on `how to create documentation for the Python Ethereum ecosystem <https://github.com/ethereum/snake-charmers-tactical-manual/blob/main/documentation.md>`_.


Pull Requests
~~~~~~~~~~~~~

It's a good idea to make pull requests early on. A pull request represents the
start of a discussion, and doesn't necessarily need to be the final, finished
submission.

GitHub's documentation for working on pull requests is `available here <https://help.github.com/articles/about-pull-requests/>`_.

Once you've made a pull request, take a look at the Circle CI build status in the
GitHub interface and make sure all tests are passing. In general pull requests that
do not pass the CI build yet won't get reviewed unless explicitly requested.

If the pull request introduces changes that should be reflected in the release notes,
please add a `newsfragment` file as explained
`here <https://github.com/ethereum/<REPO_NAME>/blob/main/newsfragments/README.md>`_.

If possible, the change to the release notes file should be included in the commit that introduces the
feature or bugfix.


Releasing
~~~~~~~~~


Final test before each release
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Before releasing a new version, build and test the package that will be released:

.. code:: sh

    git checkout main && git pull

    make package


    # Preview the upcoming release notes
    towncrier --draft


Build the release notes
^^^^^^^^^^^^^^^^^^^^^^^

Before bumping the version number, build the release notes.
You must include the part of the version to bump (see below),
which changes how the version number will show in the release notes.

.. code:: sh

    make notes bump=$$VERSION_PART_TO_BUMP$$

If there are any errors, be sure to re-run ``make notes`` until it works.


Push the release to github & pypi
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

After confirming that the release package looks okay, release a new version:

.. code:: sh

    make release bump=$$VERSION_PART_TO_BUMP$$


Which version part to bump
^^^^^^^^^^^^^^^^^^^^^^^^^^

The version format for this repo is ``{major}.{minor}.{patch}`` for
stable, and ``{major}.{minor}.{patch}-{stage}.{devnum}`` for unstable
(``stage`` can be alpha or beta).

To issue the next version in line, specify which part to bump,
like ``make release bump=minor`` or ``make release bump=devnum``. This is typically done from the
main branch, except when releasing a beta (in which case the beta is released from main,
and the previous stable branch is released from said branch).

If you are in a beta version, ``make release bump=stage`` will switch to a stable.

To issue an unstable version when the current version is stable, specify the
new version explicitly, like ``make release bump="--new-version 4.0.0-alpha.1 devnum"``