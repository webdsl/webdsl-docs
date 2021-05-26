# WebDSL Documentation

[![Build](https://github.com/webdsl/webdsl-docs/actions/workflows/main.yml/badge.svg)](https://github.com/webdsl/webdsl-docs/actions)
[![Docs](https://img.shields.io/badge/docs-latest-brightgreen)](https://webdsl.org/)
[![GitHub](https://img.shields.io/github/license/webdsl/webdsl-docs)](https://github.com/webdsl/webdsl-docs/blob/main/LICENSE)

This is the repository for the new [WebDSL](https://webdsl.org/) documentation. This documentation uses [MkDocs Material][1].

To build the pages and see edits live using Docker:

```bash
make
```

Or using Python 3:

```bash
pip install -r mkdocs_requirements.txt
mkdocs serve
```

Changes to the `main` branch are automatically deployed to Github Pages.

## Adding Pages
New pages should be added to the `mkdocs.yml` `nav` element. The first page mentioned under a section header should be some `**/index.md` without a label, and will be used as the index page for that section.


[1]: https://squidfunk.github.io/