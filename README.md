MISH
====

Mish is a basic shell environment for penetration tests. Source `.mishrc` during
an assessment to load:
- logging capabilities
- basic network discovery commands
- built-in and user-defined aliases

TL;DR
-----

**1. Check requirements:** `complete` `net` `nmap` `script`. All of them are
     usually already installed.

**2. Add this line in your usual terminal RC or alias file (`.bash_aliases`,
     `.zshrc`, ...) to automatically load mish, or directly run:

```
source path/to/mishrc
```

> Mish does nothing by default, it needs to be enabled (`start`). However, if it
  is sourced, network discovery functions (netcommands) can be used even when
  the environment is off.

**3. Initialize and start an assessment environment:**

```
mish start
```

You will be asked for the name of the environment to load. We recommend to have
one environment by assessment, so the name can be something related to your
current customer.

> Mish relies on a configuration file (`.mishconfig`) to know if the environment
   is active and on which scope. Edit it manually or with `mish config`.

**4. The list of features / options available when Mish is enabled is below.**

**5. Pause or stop the current assessment environment:**

```
mish stop
```

Behavior
--------

When mish is enabled, there are a few tasks running in background:

**Display:**
- TODO

**Logging:**
- TODO


Commands
--------

TODO

Use i3blocks addon
------------------

Copy `mishi3` to your i3blocks' command path:

```
cp mishi3 /usr/share/i3blocks
```

> For instance, if your `.i3blocks.conf` file contains the following line:
  `command=/usr/share/i3blocks/$BLOCK_NAME`, then the file should be copied to:
  `/usr/share/i3blocks`

Add this to .i3blocks.conf (you can change the color & refresh interval):

```
[mishi3]
interval=30
color=#FF0000
```

TODO
----

* [X] Mish commands' autocompletion with `complete`
* [ ] Network discovery with `nmap`
* [ ] Mish as a **Oh My Zsh** plugin
* [ ] Store findings in a DB
* [ ] Extract data from CrackMapExec's DB
* [ ] `mish config` (options: path, quiet, nocolor, anxious)
* [ ] `mish note`
