# How to check spelling locally

This how-to guide outlines the various ways you can check the spelling
of the documentation you're working on. Whether you're working on documentation
for an RTD project or Charmhub, adding this check into your workflow will help
polish your documentation and enhance your skills!

## Check spelling for documentation in RTD

Use the built-in spellcheck:

```
make spelling
```

This will re-build the documentation and run the spellchecker. If you don't
want to re-build the documentation, run:

```
make spellcheck
```

## Check spelling using Vale

If you're working on documentation that's not necessarily a part of an RTD
project, you can set up Vale to run spell checks locally. 

Install Vale:

```
sudo snap install vale
```

Create a `vale.ini` file to configure Vale's behavior and specify what rules
you want to test. Below is an example of a basic `vale.ini` file:

```
[*.{md,txt,rst,html}]

Vale.Spelling = YES
```

Check the spelling of a specific document with:
```
vale --config vale.ini <document>
```
