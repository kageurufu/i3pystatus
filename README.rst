..  Always edit README.tpl.rst. Do not change the module reference manually.

i3pystatus
==========

.. image:: https://travis-ci.org/enkore/i3pystatus.svg?branch=master
    :target: https://travis-ci.org/enkore/i3pystatus

i3pystatus is a (hopefully growing) collection of python scripts for 
status output compatible to i3status / i3bar of the i3 window manager.

- `Release Notes`_
- `Configuration`_
- `Modules`_
- `Contribute`_

Installation
------------

.. admonition:: Note

    i3pystatus requires Python 3.2 or newer and is not compatible with
    Python 2.x. Some modules require additional dependencies
    documented below (see `Modules`_).

From PyPI package `i3pystatus <https://pypi.python.org/pypi/i3pystatus>`_
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

::

    pip install i3pystatus

Packages for your OS
++++++++++++++++++++

* `Arch Linux <https://aur.archlinux.org/packages/i3pystatus-git/>`_

Release Notes
-------------

Contributors
++++++++++++

* aaron-lebo
* afics
* al45tair
* Argish42
* Arvedui
* atalax
* bparmentier
* cganas
* crwood
* dubwoc
* enkore (current maintainer)
* gwarf
* janoliver (former maintainer)
* jasonmhite
* jedrz
* jorio
* mekanix
* Mic92
* micha-a-schmidt
* naglis
* philipdexter
* sbrunner
* siikamiika
* simon04
* talwrii
* teto
* tomkenmag
* tomxtobin
* tony
* xals
* yemu
* zzatkin

next
++++

* Added `uptime`_ module
* `cpu\_usage`_: Add multicore support
* `cpu\_usage\_bar`_: Add multicore support
* `network`_: Add unknown_up setting
* `parcel`_: Document lxml dependency
* Added `network\_traffic`_ module
* `mpd`_: Play song on left click even if stopped
* Fixed issues with internet-related modules
* `battery`_: Added no_text_full option
* Unexpected exceptions are now displayed in the status bar
* `mail`_: db_path option made optional
* Core: added mouse wheel handling for upcoming i3 version
* `alsa`_: mouse wheel changes volume

3.30
++++

* Added `bitcoin`_ module
* Added `now\_playing`_ module
* Added `reddit`_ module
* Added `shell`_ module
* Core: fixed custom statusline colors not working properly (see issue #74)
* `alsa`_ and `pulseaudio`_: added optional "formated_muted"
  audio is muted.
* `battery`_: add bar formatter, add not_present_text, full_color,
  charging_color, not_present_color settings
* `disk`_: add color and round_size options
* maildir: use os.listdir instead of ls
* `mem`_: add round_size option
* `mpd`_: add color setting
* `mpd`_: add filename formatter
* `mpd`_: next song on right click
* `network`_ and `wireless`_: support interfaces enslaved to a bonding master
* `network`_: detached_down is now True by default
* `network`_: fixed some issues with interface up/down detection
* `parcel`_: added support for Itella (Finnish national postal service)
  setting. If provided, it will be used instead of "format" when the
* `temp`_: add file setting
* `temp`_: fixed issue with Linux kernels 3.15 and newer
* `temp`_: removed color_critical and high_factor options
* `text`_: add cmd_leftclick and cmd_rightclick options
* `weather`_: add colorize option
* `wireless`_: Add quality_bar formatter

3.29
++++

* `network`_: prefer non link-local v6 addresses
* `mail`_: Open email client and refresh email with mouse click
* `disk`_: Add display and critical limit
* `battery`_: fix errors if CURRENT_NOW is not present
* `battery`_: add configurable colors
* `load`_: add configurable colors and limit
* `parcel`_: rewrote DHL tracker
* Add `spotify`_ module

3.28
++++

* **If you're currently using the i3pystatus command to run your i3bar**:
    Replace ``i3pystatus`` command in your i3 configuration with ``python ~/path/to/your/config.py``
* Do not name your script i3pystatus.py or it will break imports.
* New options for `mem`_
* Added `cpu\_usage`_
* Improved error handling
* Removed ``i3pystatus`` binary
* pulseaudio: changed context name to "i3pystatus_pulseaudio"
* Add maildir backend for mails
* Code changes
* Removed DHL tracker of parcel module, because it doesn't work anymore.

3.27
++++

* Add weather module
* Add text module
* PulseAudio module: Add muted/unmuted options

3.26
++++

* Add mem module

3.24
++++

**This release introduced changes that may require manual changes to your
configuration file**

* Introduced TimeWrapper
* battery module: removed remaining\_* formatters in favor of
  TimeWrapper, as it can not only reproduce all the variants removed,
  but can do much more.
* mpd: Uses TimeWrapper for song_length, song_elapsed

Configuration
-------------

The config file is just a normal Python script.

A simple configuration file could look like this (note the additional
dependencies from `network`_, `wireless`_ and `pulseaudio`_ in this
example):

::

    # -*- coding: utf-8 -*-

    import subprocess

    from i3pystatus import Status

    status = Status(standalone=True)

    # Displays clock like this:
    # Tue 30 Jul 11:59:46 PM KW31
    #                          ^-- calendar week
    status.register("clock",
        format="%a %-d %b %X KW%V",)

    # Shows the average load of the last minute and the last 5 minutes
    # (the default value for format is used)
    status.register("load")

    # Shows your CPU temperature, if you have a Intel CPU
    status.register("temp",
        format="{temp:.0f}°C",)

    # The battery monitor has many formatting options, see README for details

    # This would look like this, when discharging (or charging)
    # ↓14.22W 56.15% [77.81%] 2h:41m
    # And like this if full:
    # =14.22W 100.0% [91.21%]
    #
    # This would also display a desktop notification (via dbus) if the percentage
    # goes below 5 percent while discharging. The block will also color RED.
    status.register("battery",
        format="{status}/{consumption:.2f}W {percentage:.2f}% [{percentage_design:.2f}%] {remaining:%E%hh:%Mm}",
        alert=True,
        alert_percentage=5,
        status={
            "DIS": "↓",
            "CHR": "↑",
            "FULL": "=",
        },)

    # This would look like this:
    # Discharging 6h:51m
    status.register("battery",
        format="{status} {remaining:%E%hh:%Mm}",
        alert=True,
        alert_percentage=5,
        status={
            "DIS":  "Discharging",
            "CHR":  "Charging",
            "FULL": "Bat full",
        },)

    # Displays whether a DHCP client is running
    status.register("runwatch",
        name="DHCP",
        path="/var/run/dhclient*.pid",)

    # Shows the address and up/down state of eth0. If it is up the address is shown in
    # green (the default value of color_up) and the CIDR-address is shown
    # (i.e. 10.10.10.42/24).
    # If it's down just the interface name (eth0) will be displayed in red
    # (defaults of format_down and color_down)
    #
    # Note: the network module requires PyPI package netifaces
    status.register("network",
        interface="eth0",
        format_up="{v4cidr}",)

    # Has all the options of the normal network and adds some wireless specific things
    # like quality and network names.
    #
    # Note: requires both netifaces and basiciw
    status.register("wireless",
        interface="wlan0",
        format_up="{essid} {quality:03.0f}%",)

    # Shows disk usage of /
    # Format:
    # 42/128G [86G]
    status.register("disk",
        path="/",
        format="{used}/{total}G [{avail}G]",)

    # Shows pulseaudio default sink volume
    #
    # Note: requires libpulseaudio from PyPI
    status.register("pulseaudio",
        format="♪{volume}",)

    # Shows mpd status
    # Format:
    # Cloud connected▶Reroute to Remain
    status.register("mpd",
        format="{title}{status}{album}",
        status={
            "pause": "▷",
            "play": "▶",
            "stop": "◾",
        },)

    status.run()

Also change your i3wm config to the following:

::

    # i3bar
    bar {
        status_command    python ~/.path/to/your/config/file.py
        position          top
        workspace_buttons yes
    }

Formatting
++++++++++

All modules let you specifiy the exact output formatting using a
`format string <http://docs.python.org/3/library/string.html#formatstrings>`_, which
gives you a great deal of flexibility.

If a module gives you a float, it probably has a ton of
uninteresting decimal places. Use ``{somefloat:.0f}`` to get the integer
value, ``{somefloat:0.2f}`` gives you two decimal places after the
decimal dot

formatp
~~~~~~~

Some modules use an extended format string syntax (the mpd module, for example).
Given the format string below the output adapts itself to the available data.

::

    [{artist}/{album}/]{title}{status}

Only if both the artist and album is known they're displayed. If only one or none
of them is known the entire group between the brackets is excluded.

"is known" is here defined as "value evaluating to True in Python", i.e. an empty
string or 0 (or 0.0) counts as "not known".

Inside a group always all format specifiers must evaluate to true (logical and).

You can nest groups. The inner group will only become part of the output if both
the outer group and the inner group are eligible for output.

TimeWrapper
~~~~~~~~~~~

Some modules that output times use TimeWrapper to format these. TimeWrapper is
a mere extension of the standard formatting method.

The time format that should be used is specified using the format specifier, i.e.
with some_time being 3951 seconds a format string like ``{some_time:%h:%m:%s}``
would produce ``1:5:51``.

* ``%h``, ``%m`` and ``%s`` are the hours, minutes and seconds without
  leading zeros (i.e. 0 to 59 for minutes and seconds)
* ``%H``, ``%M`` and ``%S`` are padded with a leading zero to two digits,
  i.e. 00 to 59
* ``%l`` and ``%L`` produce hours non-padded and padded but only if hours
  is not zero.  If the hours are zero it produces an empty string.
* ``%%`` produces a literal %
* ``%E`` (only valid on beginning of the string) if the time is null,
  don't format anything but rather produce an empty string. If the
  time is non-null it is removed from the string.
* When the module in question also uses formatp, 0 seconds counts as
  "not known".
* The formatted time is stripped, i.e. spaces on both ends of the
  result are removed.

Modules
-------

:System: `clock`_ - `disk`_ - `load`_ - `mem`_  - `cpu\_usage`_
:Audio: `alsa`_ - `pulseaudio`_
:Hardware: `battery`_ - `backlight`_ - `temp`_
:Network: `network`_ - `wireless`_
:Music: `now\_playing`_ - `mpd`_
:Websites & stuff: `weather`_ - `bitcoin`_ - `reddit`_ - `parcel`_
:Other: `mail`_ - `pyload`_ -  `text`_ 
:Advanced: `file`_ - `regex`_ - `runwatch`_ - `shell`_


alsa
++++


Shows volume of ALSA mixer. You can also use this for inputs, btw.

Requires pyalsaaudio

Available formatters:

* `{volume}` — the current volume in percent
* `{muted}` — the value of one of the `muted` or `unmuted` settings
* `{card}` — the associated soundcard
* `{mixer}` — the associated ALSA mixer


Settings:

:format:  (default: ``♪: {volume}``)
:format_muted: optional format string to use when muted (default: ``None``)
:mixer: ALSA mixer (default: ``Master``)
:mixer_id: ALSA mixer id (default: ``0``)
:card: ALSA sound card (default: ``0``)
:increment: integer percentage of max volume to in/decrement volume on mousewheel (default: ``5``)
:muted:  (default: ``M``)
:unmuted:  (default: ````)
:color_muted:  (default: ``#AAAAAA``)
:color:  (default: ``#FFFFFF``)
:channel:  (default: ``0``)
:interval:  (default: ``1``)



backlight
+++++++++


Screen backlight info

Available formatters:

* `{brightness}` — current brightness relative to max_brightness
* `{max_brightness}` — maximum brightness value
* `{percentage}` — current brightness in percent


Settings:

:format: format string, formatters: brightness, max_brightness, percentage (default: ``{brightness}/{max_brightness}``)
:backlight: backlight, see `/sys/class/backlight/` (default: ``acpi_video0``)
:color:  (default: ``#FFFFFF``)
:interval:  (default: ``5``)



battery
+++++++


This class uses the /sys/class/power_supply/…/uevent interface to check for the
battery status

Available formatters:

* `{remaining}` — remaining time for charging or discharging, uses TimeWrapper formatting, default format is `%E%h:%M`
* `{percentage}` — battery percentage relative to the last full value
* `{percentage_design}` — absolute battery charge percentage
* `{consumption (Watts)}` — current power flowing into/out of the battery
* `{status}`
* `{battery_ident}` — the same as the setting
* `{bar}` —bar displaying the percentage graphically


Settings:

:battery_ident: The name of your battery, usually BAT0 or BAT1 (default: ``BAT0``)
:format:  (default: ``{status} {remaining}``)
:not_present_text: Text displayed if the battery is not present. No formatters are available (default: ``Battery not present``)
:alert: Display a libnotify-notification on low battery (default: ``False``)
:alert_percentage:  (default: ``10``)
:alert_format_title: The title of the notification, all formatters can be used (default: ``Low battery``)
:alert_format_body: The body text of the notification, all formatters can be used (default: ``Battery {battery_ident} has only {percentage:.2f}% ({remaining:%E%hh:%Mm}) remaining!``)
:path: Override the default-generated path (default: ``None``)
:status: A dictionary mapping ('DIS', 'CHR', 'FULL') to alternative names (default: ``{'FULL': 'FULL', 'CHR': 'CHR', 'DIS': 'DIS'}``)
:color: The text color (default: ``#ffffff``)
:full_color: The full color (default: ``#00ff00``)
:charging_color: The charging color (default: ``#00ff00``)
:critical_color: The critical color (default: ``#ff0000``)
:not_present_color: The not present color. (default: ``#ffffff``)
:no_text_full: Don't display text when battery is full - 100% (default: ``False``)
:interval:  (default: ``5``)



bitcoin
+++++++


This module fetches and displays current Bitcoin market prices and
optionally monitors transactions to and from a list of user-specified 
wallet addresses. Market data is pulled from the BitcoinAverage Price
Index API <https://bitcoinaverage.com> while transaction data is pulled
from blockchain.info <https://blockchain.info/api/blockchain_api>.

Available formatters:

* {last_price}
* {ask_price}
* {bid_price}
* {daily_average}
* {volume}
* {status}
* {last_tx_type}
* {last_tx_addr}
* {last_tx_value}
* {balance_btc}
* {balance_fiat}



Settings:

:format: Format string used for output. (default: ``฿ {status}{last_price}``)
:currency: Base fiat currency used for pricing. (default: ``USD``)
:wallet_addresses: List of wallet address(es) to monitor. (default: ````)
:color: Standard color (default: ``#FFFFFF``)
:colorize: Enable color change on price increase/decrease (default: ``False``)
:color_up: Color for price increases (default: ``#00FF00``)
:color_down: Color for price decreases (default: ``#FF0000``)
:leftclick: URL to visit or command to run on left click (default: ``electrum``)
:rightclick: URL to visit or command to run on right click (default: ``https://bitcoinaverage.com/``)
:interval: Update interval. (default: ``600``)
:status:  (default: ``{'price_down': '▼', 'price_up': '▲'}``)



clock
+++++


This class shows a clock


Settings:

:format: stftime format string, `None` means to use the default, locale-dependent format (default: ``None``)
:color: RGB hexadecimal code color specifier, default to #ffffff, set to `i3Bar` to use i3 bar default (default: ``#ffffff``)
:interval:  (default: ``1``)



cpu_usage
+++++++++


Shows CPU usage.
The first output will be inacurate.

Linux only

Available formatters:

* {usage}       usage average of all cores
* {usage_cpu*}  usage of one specific core. replace "*" by core number starting at 0
* {usage_all}   usage of all cores separate. usess natsort when available(relevant for more than 10 cores)



Settings:

:format: format string. (default: ``{usage:02}%``)
:format_all: format string used for {usage_all} per core. Available formaters are {core} and {usage}.  (default: ``{core}:{usage:02}%``)
:exclude_average: If True usage average of all cores will not be in format_all. (default: ``False``)
:interval:  (default: ``5``)



cpu_usage_bar
+++++++++++++


Shows CPU usage as a bar (made with unicode box characters).
The first output will be inacurate.

Linux only

Available formatters:

* {usage_bar}       usage average of all cores
* {usage_bar_cpu*}  usage of one specific core. replace "*"
by core number starting at 0



Settings:

:format: format string (default: ``{usage_bar}``)
:interval:  (default: ``5``)



disk
++++


Gets ``{used}``, ``{free}``, ``{available}`` and ``{total}`` amount of bytes on the given mounted filesystem.

These values can also be expressed as percentages with the ``{percentage_used}``, ``{percentage_free}``
and ``{percentage_avail}`` formats.


Settings:

:format:  (default: ``{free}/{avail}``)
:path:  (required)
:divisor: divide all byte values by this value, default is 1024**3 (gigabyte) (default: ``1073741824``)
:display_limit: if more space is available than this limit the module is hidden (default: ``inf``)
:critical_limit: critical space limit (see critical_color) (default: ``0``)
:critical_color: the critical color (default: ``#FF0000``)
:color: the common color (default: ``#FFFFFF``)
:round_size: precision, None for INT (default: ``2``)
:interval:  (default: ``5``)



file
++++


Rip information from text files

components is a dict of pairs of the form:

::

    name => (callable, file)

* Where `name` is a valid identifier, which is used in the format string to access
  the value of that component.
* `callable` is some callable to convert the contents of `file`. A common choice is
  float or int.
* `file` names a file, relative to `base_path`.

transforms is a optional dict of callables taking a single argument (a dictionary containing the values
of all components). The return value is bound to the key.


Settings:

:format:  (required)
:components:  (required)
:transforms:  (default: ``{}``)
:base_path:  (default: ``/``)
:color:  (default: ``#FFFFFF``)
:interval:  (default: ``5``)



load
++++


Shows system load


Settings:

:format: format string used for output. {avg1}, {avg5} and {avg15} are the load average of the last one, five and fifteen minutes, respectively. {tasks} is the number of tasks (i.e. 1/285, which indiciates that one out of 285 total tasks is runnable). (default: ``{avg1} {avg5}``)
:color: The text color (default: ``#ffffff``)
:critical_limit: Limit above which the load is considered critical (default: ``1``)
:critical_color: The critical color (default: ``#ff0000``)
:interval:  (default: ``5``)



mail
++++


Generic mail checker

The `backends` setting determines the backends to use.


Settings:

:backends: List of backends (instances of ``i3pystatus.mail.xxx.zzz``, i.e. ``i3pystatus.mail.imap.IMAP``)
:color:  (default: ``#ffffff``)
:color_unread:  (default: ``#ff0000``)
:format:  (default: ``{unread} new email``)
:format_plural:  (default: ``{unread} new emails``)
:hide_if_null: Don't output anything if there are no new mails (default: ``True``)
:email_client: The email client to open on left click (default: ``None``)
:interval:  (default: ``5``)


imap.IMAP
~~~~~~~~~


Checks for mail on a IMAP server


Settings:

:host:  (required)
:port:  (default: ``993``)
:username:  (required)
:password:  (required)
:ssl:  (default: ``True``)
:mailbox:  (default: ``INBOX``)



maildir.MaildirMail
~~~~~~~~~~~~~~~~~~~


Checks for local mail in Maildir


Settings:

:directory:  (required)



mbox.MboxMail
~~~~~~~~~~~~~


Checks for local mail in mbox


Settings:





notmuchmail.Notmuch
~~~~~~~~~~~~~~~~~~~


This class uses the notmuch python bindings to check for the
number of messages in the notmuch database with the tags "inbox"
and "unread"


Settings:

:db_path: Path to the directory of your notmuch database (default: ``None``)



thunderbird.Thunderbird
~~~~~~~~~~~~~~~~~~~~~~~


This class listens for dbus signals emitted by
the dbus-sender extension for thunderbird.

Requires python-dbus


Settings:






mem
+++


Shows memory load

Available formatters:

* {avail_mem}
* {percent_used_mem}
* {used_mem}
* {total_mem}

Requires psutil (from PyPI)


Settings:

:format: format string used for output. (default: ``{avail_mem} MiB``)
:divisor: divide all byte values by this value, default 1024**2(mebibytes (default: ``1048576``)
:warn_percentage: minimal percentage for warn state (default: ``50``)
:alert_percentage: minimal percentage for alert state (default: ``80``)
:color: standard color (default: ``#00FF00``)
:warn_color: defines the color used wann warn percentage ist exceeded (default: ``#FFFF00``)
:alert_color: defines the color used when alert percentage is exceeded (default: ``#FF0000``)
:round_size: defines number of digits in round (default: ``1``)
:interval:  (default: ``5``)



mem_bar
+++++++


Shows memory load as a bar.

Available formatters:
* {used_mem_bar}

Requires psutil (from PyPI)


Settings:

:format: format string used for output. (default: ``{used_mem_bar}``)
:warn_percentage: minimal percentage for warn state (default: ``50``)
:alert_percentage: minimal percentage for alert state (default: ``80``)
:color: standard color (default: ``#00FF00``)
:warn_color: defines the color used wann warn percentage ist exceeded (default: ``#FFFF00``)
:alert_color: defines the color used when alert percentage is exceeded (default: ``#FF0000``)
:interval:  (default: ``5``)



modsde
++++++


This class returns i3status parsable output of the number of
unread posts in any bookmark in the mods.de forums.


Settings:

:format: Use {unread} as the formatter for number of unread posts (default: ``{unread} new posts in bookmarks``)
:offset: subtract number of posts before output (default: ``0``)
:color:  (default: ``#7181fe``)
:username:  (required)
:password:  (required)
:interval:  (default: ``5``)



mpd
+++


Displays various information from MPD (the music player daemon)

Available formatters (uses `formatp`_)

* `{title}` — (the title of the current song)
* `{album}` — (the album of the current song, can be an empty string (e.g. for online streams))
* `{artist}` — (can be empty, too)
* `{filename}` — (file name with out extension and path; empty unless title is empty)
* `{song_elapsed}` — (Position in the currently playing song, uses `TimeWrapper`_, default is `%m:%S`)
* `{song_length}` — (Length of the current song, same as song_elapsed)
* `{pos}` — (Position of current song in playlist, one-based)
* `{len}` — (Songs in playlist)
* `{status}` — (play, pause, stop mapped through the `status` dictionary)
* `{bitrate}` — (Current bitrate in kilobit/s)
* `{volume}` — (Volume set in MPD)

Left click on the module play/pauses, right click (un)mutes.


Settings:

:host:  (default: ``localhost``)
:port: MPD port (default: ``6600``)
:format: formatp string (default: ``{title} {status}``)
:status: Dictionary mapping pause, play and stop to output (default: ``{'pause': '▷', 'stop': '◾', 'play': '▶'}``)
:color: The color of the text (default: ``#FFFFFF``)
:interval:  (default: ``1``)



network
+++++++


Display network information about a interface.

Requires the PyPI package `netifaces`.

Available formatters:

* `{interface}` — same as setting
* `{name}` — same as setting
* `{v4}` — IPv4 address
* `{v4mask}` — subnet mask
* `{v4cidr}` — IPv4 address in cidr notation (i.e. 192.168.2.204/24)
* `{v6}` — IPv6 address
* `{v6mask}` — subnet mask
* `{v6cidr}` — IPv6 address in cidr notation
* `{mac}` — MAC of interface

Not available addresses (i.e. no IPv6 connectivity) are replaced with empty strings.


Settings:

:interface: Interface to obtain information for (default: ``eth0``)
:format_up:  (default: ``{interface}: {v4}``)
:color_up:  (default: ``#00FF00``)
:format_down:  (default: ``{interface}``)
:color_down:  (default: ``#FF0000``)
:detached_down: If the interface doesn't exist, display it as if it were down (default: ``True``)
:unknown_up: If the interface is in unknown state, display it as if it were up (default: ``False``)
:name:  (default: ``eth0``)
:interval:  (default: ``5``)



network_traffic
+++++++++++++++


Network traffic per interface, i.e., packets/bytes sent/received per second.

Requires the PyPI packages `psutil`.

Available formatters:

* `{interface}` — the configured network interface
* `{bytes_sent}` — bytes sent per second (divided by divisor)
* `{bytes_recv}` — bytes received per second (divided by divisor)
* `{packets_sent}` — bytes sent per second (divided by divisor)
* `{packets_recv}` — bytes received per second (divided by divisor)


Settings:

:format: format string (default: ``{interface} ↗{bytes_sent}kB/s ↘{bytes_recv}kB/s``)
:interface: network interface (default: ``eth0``)
:divisor: divide all byte values by this value (default: ``1024``)
:round_size: defines number of digits in round (default: ``None``)
:interval:  (default: ``1``)



now_playing
+++++++++++


Shows currently playing track information, supports most media players

Available formatters (uses `formatp`_)

* `{title}` — (the title of the current song)
* `{album}` — (the album of the current song, can be an empty string (e.g. for online streams))
* `{artist}` — (can be empty, too)
* `{filename}` — (file name with out extension and path; empty unless title is empty)
* `{song_elapsed}` — (Position in the currently playing song, uses `TimeWrapper`_, default is `%m:%S`)
* `{song_length}` — (Length of the current song, same as song_elapsed)
* `{status}` — (play, pause, stop mapped through the `status` dictionary)
* `{volume}` — (Volume)

Left click on the module play/pauses, right click goes to the next track.

Requires python-dbus available from every distros' package manager.


Settings:

:player: Player name (default: ``None``)
:status: Dictionary mapping pause, play and stop to output text (default: ``{'pause': '▷', 'stop': '◾', 'play': '▶'}``)
:color: Text color (default: ``#FFFFFF``)
:format: formatp string (default: ``{title} {status}``)
:interval:  (default: ``1``)



parcel
++++++


Used to track parcel/shipments.

Supported carriers: DHL, UPS, Itella

- parcel.UPS("<id_code>")
- parcel.DHL("<id_code>")
- parcel.Itella("<id_code>"[, "en"|"fi"|"sv"])
  Second parameter is language. Requires beautiful soup 4 (bs4)

Requires lxml and cssselect.


Settings:

:instance: Tracker instance, for example ``parcel.UPS('your_id_code')``
:format:  (default: ``{name}:{progress}``)
:name: 
:interval:  (default: ``60``)



pulseaudio
++++++++++


Shows volume of default PulseAudio sink (output).

Available formatters:

* `{volume}` — volume in percent (0...100)
* `{db}` — volume in decibels relative to 100 %, i.e. 100 % = 0 dB, 50 % = -18 dB, 0 % = -infinity dB
  (the literal value for -infinity is `-∞`)
* `{muted}` — the value of one of the `muted` or `unmuted` settings


Settings:

:format:  (default: ``♪: {volume}``)
:format_muted: optional format string to use when muted (default: ``None``)
:muted:  (default: ``M``)
:unmuted:  (default: ````)
:color_muted:  (default: ``#FF0000``)
:color_unmuted:  (default: ``#FFFFFF``)



pyload
++++++


Shows pyLoad status

Available formatters:

* `{captcha}` (see captcha_true and captcha_false, which are the values filled in for this formatter)
* `{progress}` (average over all running downloads)
* `{progress_all}` (percentage of completed files/links in queue)
* `{speed}` (kilobytes/s)
* `{download}` (downloads enabled, also see download_true and download_false)
* `{total}` (number of downloads)
* `{free_space}` (free space in download directory in gigabytes)


Settings:

:address: Address of pyLoad webinterface (default: ``http://127.0.0.1:8000``)
:format:  (default: ``{captcha} {progress_all:.1f}% {speed:.1f} kb/s``)
:captcha_true:  (default: ``Captcha waiting``)
:captcha_false:  (default: ````)
:download_true:  (default: ``Downloads enabled``)
:download_false:  (default: ``Downloads disabled``)
:username:  (required)
:password:  (required)
:interval:  (default: ``5``)



reddit
++++++


This module fetches and displays posts and/or user mail/messages from
reddit.com. Left-clicking on the display text opens the permalink/comments
page using webbrowser.open() while right-clicking opens the URL of the 
submission directly. Depends on the Python Reddit API Wrapper (PRAW)
<https://github.com/praw-dev/praw>.

Available formatters:

* {submission_title}
* {submission_author}
* {submission_points}
* {submission_comments}
* {submission_permalink}
* {submission_url}
* {submission_domain}
* {submission_subreddit}
* {message_unread}
* {message_author}
* {message_subject}
* {message_body}



Settings:

:format: Format string used for output. (default: ``[{submission_subreddit}] {submission_title} ({submission_domain})``)
:username: Reddit username. (default: ````)
:password: Reddit password. (default: ````)
:subreddit: Subreddit to monitor. Uses frontpage if unspecified. (default: ````)
:sort_by: 'hot', 'new', 'rising', 'controversial', or 'top'. (default: ``hot``)
:color: Standard color. (default: ``#FFFFFF``)
:colorize: Enable color change on new message. (default: ``True``)
:color_orangered: Color for new messages. (default: ``#FF4500``)
:mail_brackets: Display unread message count in square-brackets. (default: ``False``)
:title_maxlen: Maximum number of characters to display in title. (default: ``80``)
:interval: Update interval. (default: ``300``)
:status: New message indicator. (default: ``{'new_mail': '✉', 'no_mail': ''}``)



regex
+++++


Simple regex file watcher

The groups of the regex are passed to the format string as positional arguments.


Settings:

:format: format string used for output (default: ``{0}``)
:regex:  (required)
:file: file to search for regex matches
:flags: Python.re flags (default: ``0``)
:interval:  (default: ``5``)



runwatch
++++++++


Expands the given path using glob to a pidfile and checks
if the process ID found inside is valid
(that is, if the process is running).
You can use this to check if a specific application,
such as a VPN client or your DHCP client is running.

Available formatters are {pid} and {name}.


Settings:

:format_up:  (default: ``{name}``)
:format_down:  (default: ``{name}``)
:color_up:  (default: ``#00FF00``)
:color_down:  (default: ``#FF0000``)
:path:  (required)
:name:  (required)
:interval:  (default: ``5``)



shell
+++++


Shows output of shell command


Settings:

:command: command to be executed
:color: standard color (default: ``#FFFFFF``)
:error_color: color to use when non zero exit code is returned (default: ``#FF0000``)
:interval:  (default: ``5``)



spotify
+++++++


This class shows information from Spotify.

Left click will toggle pause/play of the current song.
Right click will skip the song.

Dependent on Playerctl ( https://github.com/acrisci/playerctl ) and GLib


Settings:

:format: Format string. {artist}, {title}, {album}, {volume}, and {length} are available for output. (default: ``{artist} - {title}``)
:color: color of the output (default: ``#ffffff``)



temp
++++


Shows CPU temperature of Intel processors

AMD is currently not supported as they can only report a relative temperature, which is pretty useless


Settings:

:format: format string used for output. {temp} is the temperature in degrees celsius (default: ``{temp} °C``)
:color:  (default: ``#FFFFFF``)
:file:  (default: ``/sys/class/thermal/thermal_zone0/temp``)
:interval:  (default: ``5``)



text
++++


Display static, colored text.


Settings:

:text:  (required)
:color: HTML color code #RRGGBB (default: ``None``)
:cmd_leftclick: Shell command to execute on left click (default: ``test``)
:cmd_rightclick: Shell command to execute on right click (default: ``test``)



uptime
++++++


Outputs Uptime


Settings:

:format: Format string (default: ``up {uptime}``)
:color: String color (default: ``#ffffff``)
:alert: If you want the string to change color (default: ``False``)
:seconds_alert: How many seconds necessary to start the alert (default: ``3600``)
:color_alert: Alert color (default: ``#ff0000``)
:interval:  (default: ``5``)



weather
+++++++


This module gets the weather from weather.com using pywapi module
First, you need to get the code for the location from the www.weather.com
Available formatters:

* {current_temp}
* {current_wind}
* {humidity}

Requires pywapi from PyPI.


Settings:

:location_code:  (required)
:colorize: Enable color with temperature and UTF-8 icons. (default: ``False``)
:units: Celsius (metric) or Fahrenheit (imperial) (default: ``metric``)
:format:  (default: ``{current_temp}``)
:interval:  (default: ``20``)



wireless
++++++++


Display network information about a interface.

Requires the PyPI packages `netifaces` and `basiciw`.

This is based on the network module, so all options and formatters are
the same, except for these additional formatters and that detached_down doesn't work.

* `{essid}` — ESSID of currently connected wifi
* `{freq}` — Current frequency
* `{quality}` — Link quality in percent
* `{quality_bar}` —Bar graphically representing link quality


Settings:

:interface: Interface to obtain information for (default: ``wlan0``)
:format_up:  (default: ``{interface}: {v4}``)
:color_up:  (default: ``#00FF00``)
:format_down:  (default: ``{interface}``)
:color_down:  (default: ``#FF0000``)
:detached_down: If the interface doesn't exist, display it as if it were down (default: ``True``)
:unknown_up: If the interface is in unknown state, display it as if it were up (default: ``False``)
:name:  (default: ``eth0``)
:interval:  (default: ``5``)




Contribute
----------

To contribute a module, make sure it uses one of the Module classes. Most modules
use IntervalModule, which just calls a function repeatedly in a specified interval.

The output attribute should be set to a dictionary which represents your modules output,
the protocol is documented `here <http://i3wm.org/docs/i3bar-protocol.html>`_.

To update this readme run ``python -m i3pystatus.mkdocs`` in the
repository root and you're done :)

Developer documentation is available in the source code and `here
<http://i3pystatus.readthedocs.org/en/latest/>`_.

**Patches and pull requests are very welcome :-)**

Table of contents
-----------------

.. contents:: Overview of this rather long README

