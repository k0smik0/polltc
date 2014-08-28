                             polltc_ README

Last Updated: 20131214 (k0smik0 - Massimiliano Leone)

 - This version definitely adds hfsc support
 - kiloBytes unit is used in place of kilobits
 - default traffic is also showed (while old version was showing only traffic from explicity created queues)
 - a html interface is provided: you should rename "INTERFACE-USING-TO-CHANGE.html" to "eth1.html" if you're 
   monitoring eth1, and so on.. then you must move that file in /var/tmp/polltc, since polltc script writes 
   output images there and html file reads images from same directory it is on. 
   Of course, you can link that dir within /var/www/ for any webserver purpose

<br/><br/>
Previous Updated: 20050923 (JasonB)

BACKGROUND

polltc_ is a small Perl script I wrote for gathering statistics on
the effectiveness of a given traffic control configuration under
Linux.  The script executes the `tc` binary for qdisc and class information
to obtain the necessary information.  Due to `tc` limitations the maximum
resolution is 10 seconds.

MODES OF OPERATION

polltc_ has two operational modes.  For short term diagnostics, it can be
run from the commandline where it will loop until interrupted.  `tc` will
be polled and information gathered and stored in an RRDTool database.  A
graph for the previous hour and the previous twenty-four hours is generated
using information obtained from the round robin databse (RRD).

For extended usage, polltc_ can be configured to run as a plugin for Munin,
which is a tool for monitoring the health of networked systems over a long
duration.  It will write collected information to standard output for
collection by the Munin daemon.  The standard runtime for a Munin setup is
five minutes.  No local files are updated when run as a Munin pluin.

PREREQUISITES

Perl 5.6.1
RRDs (for Perl interface to RRDTool)

CONFIGURING

Before you can start using polltc_, you need to modify a few values near
the start of the script.  Specifically, you need to specify a path where
you want files to be stored.  I access my graphs via a Web server, so I
dump all my files in a directory readable by my Web server.

You may not wish for polltc_ to create a graph when it runs every ten
seconds in diagnostic mode.  If such is the case, change $do_graph
to 0.

You must specify the path to your `tc` binary.  For testing purposes
I use my own `tc` binary in my home directory, so you will need to
change this or nothing will work.

RUNNING

Once you have changed the necesary options above, you can start using
polltc_.  The interface being probed for information is gathered from
the name of the file itself.  Create a symlink with the interface name
so polltc_ knows what interface to probe.  (eth0, ppp1, ect.)

$ ln -s polltc_ polltc_eth0

Now, you can run polltc_eth0 to gather information about your traffic
control configuration on eth0.

$ perl polltc_eth0 test &

An RRD database will be created and populated with values every 10
seconds.

If you wish to use it as a Munin plugin, you will want to symlink
into your /etc/munin/plugins directory.

\# ln -s polltc_ /etc/munin/plugins/polltc_eth0

polltc_ supports Munin 'autoconfig' and 'config' and when run without
any arguments, polltc_ will return values for the interface it is being
run against as expected by Munin.

If you wish for your traffic classes to be replaced with human readable
labels on the RRDTool graph output, you can set an environment variable.

For Munin, you would need to modify your plugin-conf.d/munin-node file
and add an entry like the following.

[polltc*]
env.names 10:High Priority.20:Low Priority

Notice that each pair is separated by . and the association is made
between the traffic control leaf's minor identifier and the readable
description by a colon.

On the commandline, you might execute polltc_ like the following.

$ names="10:High.20:Low" perl polltc_eth0 test &

You need not specify a readable name for every traffic class.

CREDITS

Thanks to Andreas Klauer for writing tc-graph.pl, from which original author (JasonB [http://blog.edseek.com/archives/author/jasonb/](http://blog.edseek.com/archives/author/jasonb/)) borrowed
the logic to determine the parent-child relationships within traffic
control class hierarchies.  Thanks to everyone who's emailed me suggestions
and bug reports!

LICENSE

This program is free software; you can redistribute it and/or modify
it under the terms of either:

a) the GNU General Public License as published by the Free Software
   Foundation; either version 1, or (at your option) any later
   version, or

b) the "Artistic License" which comes with Perl.

On Debian GNU/Linux systems, the complete text of the GNU General
Public License can be found in `/usr/share/common-licenses/GPL' and
the Artistic Licence in `/usr/share/common-licenses/Artistic'.

