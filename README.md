# 2D Standard

This repo contains "official" versions of 2D spells and feats.

The standard object structure is given at https://docs.google.com/document/d/1JmN3yyxtMsPsqc20GxxeTw9YhY3F9wwHr4dod5K15Go/edit.

The yaml style can be reproduced with `js-yaml` (https://www.npmjs.com/package/js-yaml) using the dump options `{ sortKeys: true, lineWidth: Infinity }`.

## Special Markdown Syntax

Anywhere `markdown` is allowed in a yaml, the following syntax is allowed:
```md
[type:displayed text]($source:file.key)
```

- The optional `type:` field determines how this component is to be rendered. If omitted, it defaults to `popup:`. Compilers must recognize the following types:
    - `popup`: render this component as a popup anchor
    - `link`: render this component as a normal hyperlink
- The `displayed text` field is the exact string that is displayed to the user.
- The optional `source:` field instructs the compiler which directory to look in for content. If omitted, it defaults to `2d:`, which is currently the only source compilers must recognize.
    - `2d`: the current version of the 2d standard
- The `file` field is the name of the yaml file containing the content. It may include one or more `/`'s if the file is in a subdirectory, but it may not include a suffix, which is always assumed to be `.yml`.
- The optional `key` field indicates the object's key in the yaml file. If not present, it is inferred to be the lowercase, snake_case, punctuation-free equivalent to the displayed text.

> ### Example
> `[heroes' feast]($spells)` is equivalent to `[popup:heroes' feast]($2d:spells.heroes_feast)`, which generates a popup to the _heroes' feast_ spell in `/path/to/2d/spells.yml`.

## Standard Object Structures

### Spell

```yml
name: string
description: markdown
higher_level: markdown
level: int
range: string
components: string
material?: markdown
duration: string
concentration?: boolean
casting_time: markdown
tags: string[] # includes classes, school(s), domain(s), element?, curse?, ritual?
sources: string[]
```

### Feat

Feats are simply features as described in the aforementioned standard:

```yml
name: string
cost: string
prerequisite?: markdown
description: markdown
```