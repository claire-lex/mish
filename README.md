MISH
====

Mish is a shell environment for penetration tests. Source `.mishrc` during an
assessment to load logging capabilities, built-in and user-defined aliases and
other useful features.

TL;DR
-----

1. Install requirements: `complete nmap`.

2. Add this line to source .mishrc in your usual terminal RC or alias file
  (`.bash_aliases`, `.zshrc`, ...):

```
. path/to/mishrc
```

3. Initialize and start an assessment environment:

```
mish start
```

> Mish relies on a configuration file (`.mishconfig`) to know if the environment
   is active and on which scope. Edit it manually or with `mish config`.

4. The list of features / options available when Mish is enabled is described
   below.

5. Pause or stop the current assessment environment:

```
mish stop
```

TODO
----

* [ ] Mish as a Oh My Zsh plugin