# Tiny URL redirect server

Written in pure go, configured by YAML.

## Configuration

Configuration is handled in the `config.yml`.  The rules in `config.yml` will be processed in order
until a match is found and then stop.  Each section can have sub-sections.

**TODO**:  Someday this should go to a database?

- `base` - Where to go if nothing else matches?
- `rules` - Contains the rules for redirects.
  - `prefix` - Prefix to match against.  **TODO**: Allow Regex.
  - `base` - Base URL for this rule.
  - `default` - Default path, if no additional rules match.
  - `rules` - Recursive rules.
    - Same definitions as root-level rules.

Example:

```yml
base: https://exegete.io
rules:
  - prefix: t # Matches /^t(.*)/
    # base is not necessary and will be passed in from higher up base.
    default: /tags
    rules:
      - prefix: w # Matches /^tw(.*)/
        default: /tag/wip
      - prefix: cli # Matches /^tcli(.*)/
        default: /tag/cli
      - prefix: 2 # Matches /^t2(.*)/
        base: https://two.exegete.io # Override previous base.
        default: /
        - rules:
          - prefix: s
            default: /sign_in
  - prefix: go
    default: /tag/go

```

Redirects will maintain everything after the match.  For example, if the URL is
`/t2/overlay/exegeteio` would turn into `https://two.exegete.io/overlay/exegeteio`.

