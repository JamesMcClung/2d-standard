# 2D Standard

This repo contains "official" versions of 2D spells.

The standard object structure is given at https://docs.google.com/document/d/1JmN3yyxtMsPsqc20GxxeTw9YhY3F9wwHr4dod5K15Go/edit.

The yaml style can be reproduced with `js-yaml` (https://www.npmjs.com/package/js-yaml) using the dump options `{ sortKeys: true, lineWidth: Infinity }`.

## Standard Object Structures

### Spell
```yml
name: string
description: markdown
level: int
range: string
components: string
material?: string
concentration?: boolean
casting_time: string
tags: string[] # includes classes, school(s), domain(s), element?, curse?, ritual?
sources: string[]
```
