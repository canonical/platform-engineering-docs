(contribute-to-the-rtd-site)=
# How to contribute to the RTD site

This how-to guide will go over everything you need to know in order to
contribute to the Platform Engineering RTD site. The guide covers useful
Sphinx capabilities, the overall process, and local testing. 

This document was created for the Sphinx + RTD demo on 20 February 2025.
<!-- TODO: Add a link to the recording once it's available! -->

## Useful Sphinx capabilities

For the purposes of the demo, I previewed different capabilities in Sphinx
that you may find useful when contributing to the team RTD site.

### Add a note

There are many different types of callouts and admonitions
that we can use in RTD. Below is a preview of a couple of the
admonitions I expect our team to use the most.

```{note}
This is a note admonition.
```

```{warning}
This is a warning admonition.
```

```{important}
This is an important admonition. 
```

```{seealso}
See also: [Callouts & Admonitions](https://mystmd.org/guide/admonitions)
```

### Tabs

By default, the Canonical Starter Pack has enabled the ability to use
tabs in our RTD sites. Below is an example of its implementation.

````{tabs}

```{group-tab} Tab 1
Information in the first tab.
```

```{group-tab} Tab 2
Information in the second tab.
```

````

```{seealso}
See also: [Group Tabs](https://canonical-documentation-with-sphinx-and-readthedocscom.readthedocs-hosted.com/style-guide-myst/#tabs)
```

### Terminal input and output

You may wish to show a command and the terminal output together to showcase
the command or point out something specific in the output. According to our
[style guide](https://docs.ubuntu.com/styleguide/en), we should not use
prompt marks, and we should separate commands and output when necessary. 

However, Sphinx has a functionality called `terminal` where we can include
commands and output together in an aesthetically pleasing way. An example is
included below:

```{terminal}
:input: echo "Hello world!"
Hello world!
```

The big downside to using the `terminal` directive is that the user cannot
easily copy and paste the command being showcased, so use the directive with care. 

```{seealso}
See also: [Terminal output in MyST](https://canonical-documentation-with-sphinx-and-readthedocscom.readthedocs-hosted.com/style-guide-myst/#terminal-output)
```

### Cross-referencing

You may wish to reference another document when you contribute. You will first
need to include a *target* in the document you would like to reference. For a
document written in Markdown, the target looks like

```
(target_header)=
```

To reference the target, you use the `{ref}` inline role. For instance, to reference
the target in the `documentation/index.rst` page, you can use {ref}`documentation-index`
which will use the section title by default. You may also specify the text of the
reference using {ref}`this type of syntax <documentation-index>`. Finally, you can use
[Markdown-specific syntax](documentation-index) to accomplish the same type of reference.

```{seealso}
See also: [Cross references](https://myst-parser.readthedocs.io/en/latest/syntax/cross-referencing.html)
```

### Add a figure

If you want to include an image, you can add it to the relevant directory and add it
using the `{image}` environment. Below is a demonstration with an actual image located
in this directory:

```{image} image.png
:alt: group-photo-for-demo-purposes
:align: center
```

If you want to include an image nested in a separate directory, you must include
the full path to the image (with a `/` at the front of the path!). Below is
an example from [one of our onboarding documents](tmate-debug-ssh-self-hosted-runners).

```{image} /onboarding/how-to/images/use-tmate-debug-ssh-with-self-hosted-runners-2.png
:alt: output-action
:align: center
:scale: 50%
```

```{seealso}
See also: [Images and figures](https://myst-parser.readthedocs.io/en/latest/syntax/images_and_figures.html)
```

### Footnotes

To add a footnote, you can use standard Markdown syntax. Use syntax such as
`[^note-label-name]`, adding the footnote later with the same label, a colon and
the additional description. You may also add a footnote with multiple
lines[^note].

[^note]: Remember to use proper indentation for your larger footnotes!

```{seealso}
See also: [Footnotes](https://mystmd.org/spec/footnotes)
```

## Contribute to the RTD site

Contributions to the RTD site will take on two primary forms:
1. Improving a currently existing document
2. Adding a brand new document

In either scenario, preview your changes locally, run the tests, and then
open your PR! Tag Erin as a reviewer; you may want to tag another team member
if the content requires a technical review.

### Improve a currently existing document

Whether you're on a random walk through our docs or you're looking for
a specific document, you may spot something that you want to correct or
improve upon. At the bottom of the document, there is a "Edit this page on GitHub"
button with a link that takes you directly to the document on GitHub. From there,
you can edit the page and open a pull request to propose the changes.

#### Converting from reStructuredText to Markdown

TODO: Add text here about this! It's easy! Be sure to check everything!

### Add a new document

Choose the most appropriate location for the document. For instance,
this demostration document was chosen to live under the `documentation` directory
since that's the most relevant place for this information. 

Choose a descriptive and concise name for your document. You will need this
name when you add the document to the appropriate table of contents in the
directory's `index` file. 

You must add your document to the table of contents!

## Preview and test

When testing locally, some useful commands include:

* `make help` shows all the available commands.
* `make run` builds the RTD site locally
* `make spellcheck` runs the spelling check.
* `make linkcheck` runs the link checker.
* `make woke` runs the inclusivity tests.
* `make vale` runs the Vale style checker.

### If the spell checker fails

You can add words to `.custom_wordlist.txt` to prevent unnecessary failures.

You may need to run `make clean` followed by `make html` if you find that
the spellchecker is complaining even after you've corrected a mistake.

### If the link checker fails

You can add links to ignore to `conf.py` under `linkcheck_ignore`. 

If the complaint is about due to an anchor, then you can add the
site you wish to ignore anchors on under `linkcheck_anchors_ignore_for_url`.