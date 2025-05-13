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

```ts
type Spell = {
    name: string
    description: markdown
    higher_level?: markdown
    level: uint
    range: string
    components: string
    material?: markdown
    duration: string
    concentration?: boolean = false
    casting_time: markdown
    tags?: string[] = [] // includes classes, school(s), domain(s), element?, curse?, ritual?
    sources: string[]
}
```

### Feat

Feats are simply features as described in the aforementioned standard:

```ts
type Feat = {
    name: string
    cost: int
    prerequisite?: markdown
    description: markdown
}
```

### Creature

```ts
type Creature = {
    name: string
    size: CreatureSize
    swarm_size?: CreatureSize
    type: CreatureType
    alignment?: string
    druid_level?: int
    proficiency_bonus?: int // overridden when rendered in “spell mode” to be “equals your proficiency bonus”
    ability_scores?: {
        [ability in AbilityAbbreviation]?: int = 0
    } = {}
    speeds?: {
        [speed: string]: string // e.g. "walk: 30 ft."
    } = {}
    senses?: {
        [sense: string]: string // e.g. "darkvision: 60 ft."
    } = {}
    saves?: {
        [ability in AbilityAbbreviation]?: float = 0 // final result = ability_scores[ability] + floor(proficiency_bonus * saves[ability])
    } = {} 
    skills?: {
        [skill in Skill]?: float = 0 // same calculation as saves
    } = {}
    languages?: string[] = []
    damage_resistances?: string[] = []
    damage_immunities?: string[] = []
    damage_vulnerabilities?: string[] = []
    condition_immunities?: string[] = []
    ac?: int | string = 10 + ability_scores[dex]
    hp?: uint | string = hit_dice * (average(size_to_hit_die[size]) + ability_scores[con])
    hit_dice: uint | string // if this is a string, hp must be specified
    traits?: CreatureTrait[] = []
    attacks?: CreatureAttack[] = []
    actions?: CreatureAction[] = []
    reactions?: CreatureReaction[] = []
    legendary_actions?: CreatureAction[]
    legendary_action_points?: uint | string = legendary_actions === undefined ? undefined : “1 legendary action point per player”
}

// where:

type CreatureSize = "Minuscule" | "Tiny" | "Small" | "Medium" | "Large" | "Huge" | "Gargantuan"

type CreatureType = "Aberration" | "Beast" | "Celestial" | "Construct" | "Dragon" | "Elemental" | "Fey" | "Fiend" | "Giant" | "Humanoid" | "Monstrosity" | "Ooze" | "Plant" | "Undead"

type CreatureTrait = {
    name: string
    description: contextual_markdown
}

type CreatureAttack = {
    name: string
    proximity: "melee" | "ranged"
    medium: "weapon" | "spell"
    properties?: string[] = [] // e.g. ["finesse", "thrown (20/60 ft.)"]
    hit: contextual_markdown
}

type CreatureAction = {
    name: string
    cost: int
    description: contextual_markdown
}

type CreatureReaction = {
    name: string
    cost: uint // 0 => free reaction, 1 => normal reaction
    trigger: contextual_markdown
    description: contextual_markdown
}
```

In the above, `contextual_markdown` supports everything that `markdown` supports in addition to the special "@" syntax below, which causes the item to be rendered differently depending on context. In this case, "context" means either as a typical DM-controlled creature, a creature summoned by a spell, or a druid's Wild Shape form (the latter two contexts are currently identical).
- `@save({ability_score}, {ability_score})`
    - first arg: kind of save the target makes
    - second arg: stat used to set the DC
    - renders as either e.g. “DC 14 Constitution saving throw” or “Constitution saving throw against your spell save DC”, depending on context
    - example usage: “the target makes a @save(dex, cha) or takes 8d6 fire damage”
- `@check({ability_score | skill}, {ability_score})`
    - same as `@save`, but renders as either e.g. "DC 10 Athletics check" or "Athletics check against your spell save DC", depending on context
- `@plus({ability_score})`
    - renders as either e.g. “ - 1” or “ + your spellcasting ability score”, depending on context
    - example usage: “1d8@plus(str) slashing damage”
- in all of the above, the following values are allowed:
    - ability_score: str, dex, con, int, wis, cha
    - skill: acrobatics, animal_handling, arcana, athletics, deception, history, insight, intimidation, investigation, medicine, nature, perception, performance, persuasion, religion, sleight_of_hand, stealth

## Miscellaneous Types

```ts
type Skill = "Acrobatics" | "Animal Handling" | "Arcana" | "Athletics" | "Deception" | "History" | "Insight" | "Intimidation" | "Investigation" | "Medicine" | "Nature" | "Perception" | "Performance" | "Persuasion" | "Religion" | "Sleight of Hand" | "Stealth"

type AbilityAbbreviation = "str" | "dex" | "con" | "int" | "wis" | "cha"
```
