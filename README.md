# npm2sysv

## populate a [Daemon::Control](https://github.com/symkat/Daemon-Control)-based LSB init script with your package.json!

This package generates [sysv](https://en.wikipedia.org/wiki/SYSV) init scripts,
which is historically what linux distros use to manage daemons. Just follow
the directions to hook it into your /etc/init.d directory and you should be
good to go!

(Note that these days sysv replacements such as [upstart]() and [systemd]() have
their own analogs to these, but upstart-based systems at least are backwards
compatible, and systemd probably is as well. If not, writing configs for them
are simpler than using perl and Daemon::Control *anyway*.)

Basically this just populates a template perl script with data from your
`package.json`. PRETTY STRAIGHTFORWARD

## install:

    $ npm install npm2sysv # use it in your npm scripts
    $ sudo npm install npm2sysv -g # install globally
    $ sudo cpanm Daemon::Control # see http://www.cpan.org/modules/INSTALL.html

## example:

```
josh@onix:~/dev/img2txtbot$ npm2sysv
To run your new init script:

    $ ./init.pl start

To install globally:

    $ sudo ./init.pl get_init_file > /etc/init.d/img2txtbot

For more information, see: https://github.com/symkat/Daemon-Control
josh@onix:~/dev/img2txtbot$ cat init.pl
#!/usr/bin/perl
use warnings;
use strict;
use Daemon::Control;

Daemon::Control->new({
    name        => 'img2txtbot',
    lsb_start   => '$syslog $remote_fs',
    lsb_stop    => '$syslog',
    lsb_sdesc   => '{{pkg-name}}',
    lsb_desc    => 'A node.js irc bot that uses libcaca and python to turn image links into colorful text representations. Neat!',
    path        => '/home/josh/dev/img2txtbot/init.pl',
    program     => sub {
      exec 'cd /home/josh/dev/img2txtbot && npm start';
    },
    pid_file    => '/tmp/img2txtbot.pid',
    stderr_file => '/tmp/img2txtbot.log',
    stdout_file => '/tmp/img2txtbot.log',
    fork        => 2
})->run;josh@onix:~/dev/img2txtbot$ 
```

# y u sysv init

* lowest common denominator
* pretty sure my ubuntu server already knows what to do with these

# y u perl

* someone I knew already wrote it
* you try writing a node script that can reliably double-fork. no, go ahead.

## license

MIT/X11
