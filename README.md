# 2D Standard

This repo contains "official" versions of 2D spells and feats.

The standard object structure is given at https://docs.google.com/document/d/1JmN3yyxtMsPsqc20GxxeTw9YhY3F9wwHr4dod5K15Go/edit.

The yaml style can be reproduced with `js-yaml` (https://www.npmjs.com/package/js-yaml) using the dump options `{ sortKeys: true, lineWidth: Infinity }`.

## Special Markdown Syntax

Anywhere `markdown` is allowed in a yaml, the following syntax is allowed:
```md
[displayed link text](source:file/key)
[[displayed popup text]](source:file/key)
```

- The `displayed text` field is what is displayed to the user, exactly as-is. No markdown syntax is allowed within.
- The number of square brackets, `[]` or `[[]]`, suggests whether a link or a popup should be generated, respectively.
- The optional `source` field defaults to `2d`, which indicates that the content exists in the 2d standard. Arbitrary sources are permitted, but the expectation is that eventually all data will be standardized.
- The `file` field indicates which yaml file the content exists in.
- The optional `key` field indicates the object's key in the yaml file. If not present, it is inferred to be the lowercase, snake_case, punctuation-free equivalent to the displayed text.

> ### Example
> The following is equivalent to `[[heroes' feast]](2d:spells/heroes_feast)`, which generates a popup anchor to the _heroes' feast_ spell:
> ```md
> You cast [[heroes' feast]](spells).
> ```

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