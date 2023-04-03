MISH
====

Mish is a basic shell environment for penetration tests. Enable `mishrc`'s
environment during an assessment to load:
- logging capabilities
- changes to terminals' display
- basic network discovery commands
- built-in (and user-defined) aliases

> Mish is nothing but a RC file with few dependencies that provides a light
  penetration test environment with basic logging and help features. You can use
  it with whatever penetration tools and environment you usually use.

TL;DR
-----

**1. Check requirements:** `complete` `net` `nmap` `script`. All of them are
     usually already installed.

**2. Add this line in your usual terminal RC or alias file (`.bash_aliases`,
     `.zshrc`, ...) to prepare mish for loading**:

```
source path/to/mishrc
```

Mish does nothing by default, it needs to be enabled (`start`). However, if it
is sourced, network discovery functions (netcommands) can be used even when the
environment is off.

**3. Initialize and start an assessment environment:**

```
mish start
```

> You will be asked for the name of the environment to load. We recommend to
  have one environment by assessment, so the name can be something related to
  your current customer.

**4. The environment enables display and logging functions (details in
  Behavior).**

* Create a dedicated history file
* Store script records of all terminals
* Change terminal prompts
* Provide network discovery basic functions

> The i3blocks addon `mishi3` can be used to know when mish is enabled, see
  below.

**5. Pause or stop the current assessment environment:**

```
mish stop
```

Behavior
--------

Here is what happens in background when mish is enabled:

1. Start logging features with a dedicated history and a record of everything
   that happens in the terminal :

   * Every command entered in the terminal is stored in a dedicated history file
     along with the command's start date and time.

   * The command "script" is run in the current terminal and any new terminal
     that is opened, meaning that everything that is printed to these terminals
     are logged. One file per terminal is created.

> Log and history files are stored in the directory defined at setup.

2. Change the terminal prompt. By default, it just adds the name of the
   assessment in front of the current prompt.

> The i3blocks addon `mishi3` can be used to know when mish is enabled, see
  below.
  
Commands
--------

When it is sourced, mish also gives access to a few commands, even when the
environment is not loaded.

```
$> mish help

Usage: mish [help start status stop ping host]

 commands:
help	Display this help
start	Enable mish environment
status	Get information about mish's current status
stop	Disable mish environment

 netcommands (call to mishnet):
ping    Run ping on a range
host	Run host on a range

 options to netcommands:
-n 	Do not store results to a file
-o name Change the name of the output file (default: <range>.<date>.<cmd>.mish): 
-t num  Number of threads to use (default is 256)

Usage: mish [ping host] [-n -o [outfile] -t [threadnumber]]
These netcommands run a basic network discovery commands on a range
(multithreaded) and output the results to a file (by default).

```

> Unless specified with option `-n`, the result of every command that is printed
  to stdout is also stored in a file in the `traces` directory.

### Network discovery basic functions (netcommands)

Mish has functions to do very basic network-related stuff, but on a range.

* **ping** on a range (one ping request sent per host):

```
mish ping 192.168.1.0/24
```

* **host** on a range (the command host is called for all IPs in range):

```
mish host 192.168.1.0/24
```

Option `-n` can be used with any command to avoid storing the output to a
file. Otherwise, the results are stored like this:

```
$> cat 192.168.1.1_24.20230403-144038.ping.mish
192.168.1.1
192.168.1.20
192.168.1.73
192.168.1.14
```

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

* [ ] Automated logging for `nmap`
* [ ] Store basic results in a DB
* [ ] `mish note`
* [ ] Be able to change more things from the configuration file (e.g.: folder
  names, prompt style, enable and disable things)
* [ ] `mish config`
* [ ] Checklist system?
* [ ] Mish as a **Oh My Zsh** plugin?
