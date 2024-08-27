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

**1. Check requirements.**

Base pack:
`complete` `net` `nmap` `script` `nc`. All of them are usually already installed.

For the screenshot capabilities:
`apt install imagemagick rdesktop chromium-browser vncsnapshot`

**2. Add this line in your usual terminal RC or alias file (`.bash_aliases`,
     `.zshrc`, ...) to prepare mish for loading**.

```
source path/to/mishrc
```

Mish does nothing by default, it needs to be enabled (`start`). However, if it
is sourced, network discovery functions (netcommands) can be used even when the
environment is off.

**3. Initialize and start an assessment environment.**

```
mish start
```

> You will be asked for the name of the environment to load. We recommend to
  have one environment by assessment, so the name can be something related to
  your current customer.

**4. The environment enables display and logging functions (details in
  [Behavior](#behavior)).**

* Create a dedicated history file
* Store script records of all terminals
* Increase logging and traces storage for some commands
* Change terminal prompts
* Provide network and AD discovery basic functions

> The i3blocks addon `mishi3` can be used to know when mish is enabled, see
  below.

**5. Pause or stop the current assessment environment.**

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

   * The command "nmap" has an additionnal wrapper so that every nmap command
     generates timed nmap and grapable output files in the `traces` directory.

2. Change the terminal prompt. By default, it just adds the name of the
   assessment in front of the current prompt.

### Traces

Log and history files are stored in `logs` in the directory defined
at setup.

Mish net commands (detailed below) and the nmap wrapper create additional file
stored in the `traces` folder.

The files in `traces` are in a subfolder named after the origin IP address of
the command (i.e. your host's IP address). This is useful if you are auditing
several scopes from different VLANs. For instance:

- First audit day, I am in the user VLAN with IP 192.168.1.25. My traces go to
  `<mish_path>/traces/192.168.1.25/`.
- Second audit day, I test from the admin VLAN with IP 192.168.100.56. My traces
  go to `<mish_path>/traces/192.168.100.56/`.

> If the origin IP address cannot be retrieved, the files are stored directly in
  the `traces` folder.

Commands
--------

When it is sourced, mish also gives access to a few commands, even when the
environment is not loaded.

```
$> mish help

Usage: mish [help start status stop show list ping host screen web vnc rdp admin]

 commands:
help	Display this help
start	Enable mish environment
status	Get information about mish's current status
stop	Disable mish environment

 netcommands (call to mishnet):
show	Display a collection of information about the current network
list    List all IP addresses in a range
ping    Run ping on a range
host	Run host on a range
screen	Screenshot web, RDP and VNC screens on a range
	separated commands to run only one option: web, vnc, rdp
	additional args: -p [VNC pwd], --ports [web ports (default: 80,443,8000,8080)]
admin   Extract the list of local administrators on Windows hosts on a range
	additional args: -d [domain] -u [username] -p [password] [-en]

 options to netcommands:
-n 	Do not store results to a file
-o name Change the name of the output file (default: <range>.<cmd>.<date>.mish): 
-t num  Number of threads to use (default is 256)

Usage: mish [show list ping host screen web vnc rdp admin] [-n -o [outfile] -t [threadnumber]]
These netcommands run basic network discovery commands on a range
(multithreaded) and output the results to a file (by default).
```

Unless using option `-n`, the result of every mish command (see below) printed
to stdout is also stored in a file.

- When the environment is off, the file is created in the current working directory.
- When the environment is on, the file is created in the `traces` directory.

By default, the file created has a name such as
`<range>.<command>.<date>.mish`. It can be changed with option `-o`.

### Network discovery basic functions (netcommands)

Mish has functions to do very basic network-related stuff.

* **show** network information, combining the output of several commands (nmcli,
  nslookup, nbtns, etc.) to find out where we just landed:

```
mish show
```

* **ping** on a range (one ping request sent per host):

```
mish ping 192.168.1.0/24
```

* **host** on a range (the command host is called for all IPs in range):

```
mish host 192.168.1.0/24
```

* **screen** on a range for the web, RDP and VNC services. Details below.

```
mish screen 192.168.1.0/24
```

* **admin** on a range to list the local administrators on each host via
    RPC. This one requires domain account credentials, the account does not need
    to be privileged.

```
mish admin 192.168.1.0/24 -d domain.local -u username -p password
```

#### Screen

`mish screen` takes screenshots on a range for the web, RDP and VNC services all
at once. The separate commands `mish web`, `mish rdp` and `mish vnc` can be used
as well.

- Option `-p/--password` can be used to specify a VNC password.
- Option `--ports` can be used to specify the port for the web services
  (default: 80,443,8000,8080).

```
mish screen 192.168.1.0/24 -p MYVNCPASS
```

Screenshots are stored in a folder named `screens` in the `traces` directory if
mish environment is enabled, and in the current working directory otherwise.
Unless `-n` is specified, a file containing the summary of screenshots taken and
open ports will be created as well (`traces` or current working directory).

> RDP screenshots do not work if Network Level Authentication (NLA) is
  enabled, but mish at least tells you if the RDP port open.

This command requires more dependencies than the others, namely:

- `mish web`: `chromium`
- `mish rdp`: `rdesktop`, `nc`, `import` (`imagemagick`)
- `mish vnc`: `vncsnapshot` (with `vncpasswd`), `nc`

**Please consider the following information if you want to limit the number of
  network requests sent**:

Each screenshot takes two steps:

- Check if the service is open (`curl` for web and `nc` for RDP and VNC)
- Run the command to take the screenshot (`chromium`, `rdesktop`, `vncsnapsot`)

For web services, the 4 default ports are checked both against HTTP and HTTPS,
with a total of 8 calls to `curl`. If you specify `n` ports (option `--ports`,
the number of calls to `curl` will be `n*2`.

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

Note
----

This tool reflects my own pentesting habits and may not be suitable for
yours. However if you like some features of the tool you are encouraged to fork
it and make it work for you. I will be glad to help.

Also, if you have suggestions, please share them!