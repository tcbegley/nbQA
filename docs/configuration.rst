.. _configuration:

Configuration
-------------

You can configure :code:`nbQA` either at the command line, or by using a :code:`pyproject.toml` file. We'll see some examples below.

.. note::
    Please note that if you pass the same option via both the :code:`pyproject.toml` file and via the command-line, the command-line will take precedence.

Extra flags
~~~~~~~~~~~

If you wish to pass extra flags (e.g. :code:`--extend-ignore E203` to :code:`flake8`) you can either run

.. code-block:: bash

    nbqa flake8 my_notebook.ipynb --extend-ignore E203

or you can put the following in your :code:`pyproject.toml` file

.. code-block:: toml

    [tool.nbqa.addopts]
    flake8 = [
        "--extend-ignore=E203"
    ]

Allow mutations
~~~~~~~~~~~~~~~

By default, :code:`nbQA` won't modify your notebooks. If you wish to let your third-party tool modify your notebooks, you can
either pass the :code:`--nbqa-mutate` flag at the command-line, e.g.

.. code-block:: bash

    nbqa black my_notebook.ipynb --nbqa-mutate

or you can put the following in your :code:`pyproject.toml` file

.. code-block:: toml

    [tool.nbqa.mutate]
    black = true

.. note::
    If you let :code:`nbQA` mutate your notebook, then trailing newlines will be removed from cells.

Cell magics
~~~~~~~~~~~

By default, :code:`nbQA` will ignore line magics (e.g. :code:`%matplotlib inline`), as well as most cell magics.
To process code in cells with cell magics, you can use the :code:`--nbqa-process-cells` CLI argument. E.g. to process code within :code:`%%add_to` cell magics, use

.. code-block:: bash

    nbqa black my_notebook.ipynb --nbqa-process-cells add_to

or use the :code:`process_cells` option in your :code:`pyproject.toml` file:

.. code-block:: toml

    [tool.nbqa.process_cells]
    black = ["add_to"]

Include / exclude
~~~~~~~~~~~~~~~~~

To include or exclude notebooks from being processed, we recommend using ``nbQA``'s own ``--nbqa-files`` and ``--nbqa-exclude`` flags.
These take regex patterns and match posix-like paths, `exactly like the same options from pre-commit <https://pre-commit.com/#regular-expressions>`_.
These can be set from the command-line with the ``--nbqa-files`` and ``--nbqa-exclude`` flags, or in your ``.pyproject.toml`` file in the
``[tool.nbqa.files]`` and ``[tool.nbqa.exclude]`` sections.

Say you're running ``nbqa isort`` on a directory ``my_directory``. Here are some examples of how to include/exclude files:

- exclude notebooks in ``my_directory`` whose name starts with ``poc_``:

    .. code-block:: toml

        [tool.nbqa.exclude]
        isort = "^my_directory/poc_"

- exclude notebooks in subdirectory ``my_directory/my_subdirectory``:

    .. code-block:: toml

        [tool.nbqa.exclude]
        isort = "^my_directory/my_subdirectory/"

- only include notebooks in ``my_directory`` whose name starts with ``EDA``:

    .. code-block:: toml

        [tool.nbqa.files]
        isort = "^my_directory/EDA"

All the above examples can equivalently be run from the command-line, e.g. as

.. code-block:: bash

    nbqa isort my_directory --nbqa-exclude ^my_directory/poc_

Don't skip bad cells
~~~~~~~~~~~~~~~~~~~~

By default, ``nbQA`` will skip cells with invalid syntax. To process cells with syntax errors, you can use the :code:`--nbqa-dont-skip-bad-cells` CLI argument.

This can be set from the command-line with the ``--nbqa-dont-skip-bad-cells`` flag, or in your ``.pyproject.toml`` file in the
``[tool.nbqa.dont_skip_bad_cells]`` section.

For example, to process "bad" cells when running ``black`` on ``notebook.ipynb``, you could
add the following to your :code:`pyproject.toml` file:

    .. code-block:: toml

        [tool.nbqa.dont_skip_bad_cells]
        black = true

or, from the command-line:

.. code-block:: bash

    nbqa black notebook.ipynb --nbqa-dont-skip-bad-cells

Skip cells based on celltags
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can skip cells based on the celltags in their metadata using the :code:`--nbqa-dont-skip-bad-cells` CLI argument.

For example, to skip cells which contain either the ``skip-flake8`` or ``flake8-skip`` tags, you could
add the following to your :code:`pyproject.toml` file:

    .. code-block:: toml

        [tool.nbqa.skip_celltags]
        black = ["skip-flake8", "flake8-skip"]

or, from the command-line:

.. code-block:: bash

    nbqa black notebook.ipynb --nbqa-skip-celltags=skip-flake8,flake8-skip
