README.md

OUTPUT:

http://nemo-docs.readthedocs.io/en/latest/?#

PURPOSE:

Managing NEMO logs, documents and how-to guides.
Using Sphinx markdown, with GitHub and Readthedocs.

How to edit the docs:

* cd /Users/jeff/GitHub/NEMO_docs/docs
* Edit the *rst files: http://www.sphinx-doc.org/en/stable/theming.html. Really good *.rst cheatsheet https://github.com/ralsina/rst-cheatsheet/blob/master/rst-cheatsheet.rst
* Preview the *.rst files
* $ make html
* Commit change to github and sync NEMO-docs:
* Check files in readthedocs: http://nemo-docs.readthedocs.io/en/latest/?#

Use Sphinx rest markdown in Atom editor. This supports live rest markdown preview: https://atom.io/packages/rst-preview-pandoc.
You have to:

* install pandoc. e.g. using homebrew: brew install pandoc.

* Install atom package: language-reStructuredText language-sphinx 0.1.6
* also install rst-preview-pandoc
* Need to change the Atom settings for rst-preview-pandoc, so it knows the panic path e.g. /usr/local/bin
