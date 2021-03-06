# Meeting Notes

Meeting notes of past WebDSL meetings are documented here.

## WebDSL Improvement Meeting May 2021

The following points were discussed in a WebDSL improvement meeting.

#### Documentation

- A new documentation website using MkDocs Material, organized according to https://documentation.divio.com/.
- Port all relevant material from the current documentation to the new style and organize accordingly.
- Introduce a new getting-started guide, inspired by the guide written for the Web Programming Languages course.

#### Development Experience

- Finish the WebDSL SDF3 and Statix implementation to provide a new, modernized and robust WebDSL front-end.
- Modernize the WebDSL back-end: get analysis information in Stratego from a Stratego-Statix API, split up transformation tasks in more modular fashion to prepare for incrementalization.

#### New Language Features

- Introduce more client-side features or constructs in order to maintain WebDSL's consistency when writing interactive web apps.
- Introduce the notion of transactions in WebDSL in order to better integrate with external services.

#### Enhancement Proposals

##### Syntax

- Add Syntactic sugar for easily usable page routing customization.
- Reduce Inconsistencies in the existing syntax:
    - Both `.length` and `.length()` are used for different types.
    - Optional semicolons in template code vs. required semicolons in action code.
    - Optional brackets for most definitions (templates and actions) but required brackets when calling an action.
    - Notion of sections is unnecessary.

##### Improve Built-in Functionality

- Improve interoperability of built-in types such as Date/DateTime/Time, Integer/Float/Long and File/Image.
- Introduce missing built-in inputs: `inputajax` for `Secret` and `WikiText` types.
- Reduce the effort necessary to delete existing entities that have relations.

##### New Language Constructs

- Introduce break/continue for loops and support early returns in services.
- Introduce conditionals in template attributes.
- Introduce locally overridable UI attributes.
- Introduce the notion of middleware that can for example validate or modify incoming requests.

##### Lucene Search

- Support searching for true substrings (\*query\*).
- Support the ability to chain search conditions on multiple fields.

##### Development Environment

- Introduce a containerized environment to build and run WebDSL apps.
- Reduce the effort necessary to set up a project due to configuration settings in `application.ini`. Certain database modes would not work on certain operating systems, the default one has to work on all platforms.
