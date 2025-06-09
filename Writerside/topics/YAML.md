# YAML

**YAML** stands for **"YAML Ain't Markup Language"**.  
It is a human-readable data serialization format commonly used for configuration files and data exchange between
languages. YAML is widely used in modern software tools and infrastructure. Files typically have `.yaml` or `.yml`
extensions.

## Basic Data Types

YAML supports several standard data types:

- **Integers**: `15`, `123`
- **Strings**: `"15"`, `'Hello, YAML!'` (use single or double quotes)
- **Floats**: `15.033`
- **Booleans**: `true`, `false`
- **Null**: `null`

YAML tries to auto-detect types. To explicitly declare a type, use `!!`.  
Example: `!!str yes` forces YAML to treat `yes` as a string.

## Maps (Key–Value Pairs)

YAML maps are structured as key-value pairs.

```yaml
object: Book

metadata:
  name: Three Men in a Boat
  author: Jerome K Jerome
  genre: humorous account

published:
  year: 1889
  country: United Kingdom
````

* The `---` line is optional unless you're defining multiple documents.
* Keys and values are separated by a colon `:`, followed by a space.
* Use **spaces** for indentation, **not tabs**.

## Lists (Sequences)

YAML supports block-style and flow-style lists.

### Block Style:

```yaml
animals:
  - cat
  - dog
  - bird
```

Each list item starts with a `-` followed by a space.

### Flow Style:

```yaml
animals: [ cat, dog, bird ]
```

## Combining Maps and Lists

YAML allows nesting of maps and lists.

```yaml
weekend:
  saturday:
    - order cleaning
    - order a pizza
    - watch new series
  sunday:
    - go to yoga
    - hang out with a friend
```

You can nest lists inside maps, maps inside lists, and so on.

## Multi-line Strings

Use `|` to preserve newlines in multi-line strings.

```yaml
saturday: |
  order cleaning
  order a pizza
  watch new series
```

## Comments

Use `#` to add comments.

```yaml
# This is a comment
metadata: # inline comment
  name: Three Men in a Boat
  author: Jerome K Jerome
  genre: humorous account
```
