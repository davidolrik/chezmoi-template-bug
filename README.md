# Unable to call templates in `.chezmoi.toml.tmpl`

When calling the template function in `.chezmoi.toml.tmpl` a template "template name" not defined error is thrown.

In this repo I've tried to remove everything but the error.

There are only 2 files:

- `.chezmoi.toml.tmpl` which calls the template `templateTest`
- `.chezmoitemplates/templateTest` - A simple template that only outputs a single line

## The Files

### `.chezmoi.toml.tmpl`

```tmpl
{{ template "templateTest" . }}

```

### `.chezmoitemplates/templateTest`

```tmpl
# This is a test

```

## The Error

template "templateTest" not defined

```sh
% sh -c "$(curl -fsLS get.chezmoi.io)" -- init --apply https://davidolrik@github.com/davidolrik/chezmoi-template-bug.git
info found version 2.42.3 for latest/linux/amd64
info installed ./bin/chezmoi
Cloning into '/home/test/.local/share/chezmoi'...
remote: Enumerating objects: 8, done.
remote: Counting objects: 100% (8/8), done.
remote: Compressing objects: 100% (5/5), done.
remote: Total 8 (delta 1), reused 4 (delta 0), pack-reused 0
Receiving objects: 100% (8/8), done.
Resolving deltas: 100% (1/1), done.
chezmoi: template: chezmoi.toml:1:12: executing "chezmoi.toml" at <{{template "templateTest" .}}>: template "templateTest" not defined
```

## Debugging

Executing the template directly works as expected

```sh
% ./bin/chezmoi execute-template < .local/share/chezmoi/.chezmoi.toml.tmpl
# This is a test
```

## Solution

This is intended behavior as the `.chezmoi.toml.tmpl` is executed before the source state is read, so templates in `.chezmoitemplates` are not yet available.

Documented by [#3419](https://github.com/twpayne/chezmoi/pull/3419).
