# dcode://

This repo describes and implements a URL scheme to share code.

# Link-to-code

A dCode URL represents a location in a codebase.

A dCode handler finds this code on a particular computer and opens it with the user's preferred editor.

URLs can be shared within a team, embedded in documentation, tickets, comments, or simply stored as personal bookmarks. Same as HTTP.

URLs support different degrees of precision and are capable of describing what a location means across code changes. It can mean "this exact place and time" or "this function definition in the latest master".

## Strict mode

A dCode URL that contains a repository URL, commit ID, path, line and column numbers uniquely identifies a location. It may still contain other details for readability, or to allow friendly alternative behaviors of handlers.

## Human-friendly mode

URLs may omit all parts but the path. This makes it easy to write them by hand or to generate them from other tools. Handlers should make their best efforts to locate the code that best matches the URL.

# Examples

Absolute minimum. When referring to a file, simply add "dcode colon triple slash" in front of it.

    dcode:///some_pretty_specific/filename.js

If the path is not unique enough (like main.js), add the repo name between the slashes:

    dcode://my_project/main.js

Add basic in-file location:

    dcode://my_project/main.js?line=12

Make it robust to changes using words. The handler can be smart and find it even after the code has changed, within reason:

    dcode://my_project/main.js?line=12&column=4&word=MyComponent,function

Make it unambiguous across spacetime, complete with git url and commit id.

    dcode://my_project/README.md?git=git%40github.com%3Adeckardai%2Fdcode.git&h=63d2f71&line=12&column=3

Of course, those would rather be generated by tools, for instance editor plugins.


# Specification

A dCode URL is composed of a mandatory path and optional repository and file information (spaces for readability):

    dcode:// repo_name / path ? repo_spec & code_spec

`path`: the path to a file relative to the root of a project, typically a git repository.

`repo_name`: the common name of the repo, the last part of a repo url and typically the name of the local working directory.

`repo_spec` can indicate version control details, such as branch names and commit ids.

`code_spec` can indicate the location of code inside of a file.

Parameters, if present, should appear in the following order, so that people can learn to read them quickly.

## Repository specification

`git`: a URL that can be interpreted by git. Must be URL-encoded.

`hg`: a URL that can be interpreted by mercurial. Must be URL-encoded.

`commit` or `h`: a specific commit hash.

`branch` or `b`: a branch or tag.

`date` or `d`: a date as YYYY-MM-DD, representing the last commit of the day in UTC, in the specified or the default branch.

## Code specification

`line` or `l`: line number starting at 1.

`column` or `c`: column number starting at 1.

`word` or `w`: one or more words that should appear at the URL location. Several words can be given, separated by commas. Words should be interpreted as case-insensitive, unless they explicitely contain an uppercase letter. This allows handlers to find code across changes.

## Name

A name or comment can be added at the end of URLs like this: `#--Some_name`. This makes links easier to manage.


# Implementation

The current implementation looks for all git or mercurial repositories in the home directory, by default. It can open files at the right place in any code editor. A couple of presets are provided. Contributions welcome!


# Install

    git clone https://github.com/deckardai/dcode.git
    cd dcode

    # For Linux
    make install-linux test-linux

    # For Mac
    make install-mac test-mac

This will install the url handler and open a test file after a few seconds.
DCode will likely select a basic text editor.

Edit `~/.dcode.json` to choose your actual IDE.

You should now be able to click links, like on this [test page](tests/some_file.html), and end up right in your text editor.
