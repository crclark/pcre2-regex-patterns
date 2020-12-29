## Intro

A trick to add reusable subroutines to your grep experience. Works with anything that uses PCRE2, and possibly with other regex libs, too. This simply prepends a lot of subroutine definitions to my regular expressions.

At the moment, this mainly contains the regex patterns from Logstash's Grok.

### Example

```bash
echo '2021-01-12T03:34:25.123Z WARN failed to connect' | rge '(?&TIMESTAMP_ISO8601) (?&LOGLEVEL)'
```

### To set up:

```bash
cat regex-lib | tr -d '\n' > ~/regex-lib
```

then define a function in `.bashrc`:

```bash
function rge {
  RGE_PREAMBLE=`cat ~/regex-lib`
  rg --pcre2 "${RGE_PREAMBLE}${1}"
}
```

### To use:

```bash
echo '127.0.0.1' | rge "(?&IP_LOOPBACK)"
```

### Supported tools

Tested with `pcre2grep` and `rg`.

It kind of works with `ag`, though you must be careful because `ag` uses PCRE 8, which treats subroutines as atomic groups. That means that it won't backtrack within subroutines if the match fails later on. Some subroutines are broken in `ag` because of this.
