# Control Repository Example

This repository is a prototype of the contents of a *control repository* used by a deconst system. Its role is to specify in plain, human-readable and human-editable text, what content is rendered at any particular presented URL and with what layout. Content mapping is specified by *map files*, while layouts are specified by *layout files*. The layouts themselves are also stored in this repository as [Handlebars templates](http://handlebarsjs.com/) in the `layouts/` directory.

## Map File Format

[`content.map.txt`](/content.map.txt) is a concrete example of the content mapping format.

Comments start with a `#` character. Whitespace-only lines are ignored.

### Domains

Domains are introduced on lines of their own, between square brackets:

```
[foo.example.com]
```

You may introduce a new domain at any time and it will be active until the end of the file or another domain is specified. You must specify at least one in each file before you introduce any mappings.

### Mappings

Each mapping consists of:
* The presented path prefix; the location on the final, rendered site at which this content will be available.
* The "content ID" prefix of the content to attach here. Generally, this will be the base URL of the git repository that houses the raw content.

Separate these two by whitespace on a single line per mapping. Here are two examples:

```
/  https://github.com/deconst/deconst-docs/
/python-guide/  https://github.com/kennethreitz/python-guide/
```

Path prefixes should start with a `/` and end with a `/`; content ID prefixes should end with an `/`. If you forget, though, it's okay, they'll be normalized that way during parsing.

The *longest path prefix* in the mapping file that matches a specific request URL will be applied to generate the corresponding content ID. For example, given the mappings above:

1. `GET /running/architecture` will render `https://github.com/deconst/deconst-docs/running/architecture`.

2. `GET /python-guide/writing/gotchas` will render `https://github.com/kennethreitz/python-guide/writing/gotchas`.

### Multiple Files

As your sites grow, you may distribute your mappings across many files and many directories within the mapping repository. All files should end with the ".map.txt" suffix.

This is purely for the organizational convenience of humans! The mapping service will simply concatenate them all to generate the final map.

It's an error to attempt to map anything to the same path prefix twice. This is to keep you from clobbering a previous mapping without realizing it!

## Layout File Format

The layout file format is very similar to the [map file format](#map-file-format). See [developer.rackspace.com.layout.txt](/developer.rackspace.com.layout.txt) for an example.

The only difference between the map file format and the layout file format is that the layout file format has an additional parameter on each non-domain line that represents the layout name (which comes from the metadata envelope). The layout name is the second parameter on the line, between the presented path prefix and the location of the layout relative to the `layouts` directory in this repo.

