# ![PM2](https://github.com/unitech/pm2/raw/master/pres/top-logo-wo.png)

pm2 is a process manager for Node apps with a builtin load-balancer.

### Tech notes

pm2 is perfect when you need to spread your stateless NodeJS code accross all CPUs available on a server, to keep all processes alive forever and to 0s reload them.

### Main features

- Builtin load-balancer (using the native cluster module)
- Script daemonization
- 0s downtime reload for Node
- Startup scripts for Ubuntu/CentOS (use updaterc.d for Ubuntu and chkconfig for others)
- Stop unstable process (avoid infinite loop)
- Restart on file change with --watch
- Monitoring in console
- HTTP API
- [Remote control and real time interface API](https://github.com/Unitech/pm2-interface)

Tested with Node v0.11, v0.10 (https://travis-ci.org/Unitech/pm2).

**Recommanded NodeJS version is v0.11.10**

Compatible with CoffeeScript.
Works on Linux & MacOS.

[![Build Status](https://david-dm.org/Unitech/pm2.png)](https://david-dm.org/Unitech/pm2)
[![NPM version](https://badge.fury.io/js/pm2.png)](http://badge.fury.io/js/pm2)
[![Donate](http://gravaco.in/b06327acf5ae1a2ce3f08254ed7f33d7.png)](http://goo.gl/sdaIwX)

[![NPM](https://nodei.co/npm/pm2.png?downloads=true)](https://nodei.co/npm/pm2.png?downloads=true)

### Build Status

Master: [![Build Status](https://api.travis-ci.org/Unitech/pm2.png?branch=master)](https://travis-ci.org/Unitech/pm2)

Development: [![Build Status](https://api.travis-ci.org/Unitech/pm2.png?branch=development)](https://travis-ci.org/Unitech/pm2)

## Monitoring dashboard

![Dashboard](http://leapfrogui.com/controlfrog/img/cf-layout-1.png)

We gonna release a very nice product, a dashboard to monitor every part of your NodeJS applications. Here are some links :

- [Pitch + Survey](https://docs.google.com/forms/d/1FuCjIhrGg-ItxInq2nLreoe9GS-gZWJNkNWE0JJajw8/viewform) People who fill the survey will be eligible for free license
- [Newsletter](http://signup.pm2.io/) Subscribe to be kept informed

Thanks in advance and we hope that you like pm2 !

------

## Table of contents

### Quick start

- [Installation](#a1)
- [Usage](#a2)
- [Examples](#a3)
- [Differents ways to launch a process](#a667)
- [Options](#a987)

### Features

- [Transitional state of apps](#a4)
- [Process listing](#a6)
- [Monitoring CPU/Memory usage](#a7)
- [Logs management](#a9)
- [Clustering](#a5)
- [Watch & Restart](#a890)
- [Reloading without downtime](#a690)
- [Make PM2 restart on server reboot](#a8)
- [JSON app declaration](#a10)

### Specific

- [Specific features](#a77)
- [Configuration file](#a989)
- [Enabling Harmony ES6](#a66)
- [CoffeeScript](#a19)
- [Testing PM2 on your prod environment](#a149)
- [JSON app via pipe](#a96)

### Knowledge

- [Transitional state of apps](#a4)
- [Setup PM2 on server: tutorial](#a89)
- [Logs and PID files](#a34)
- [Execute any script: What is fork mode ?](#a23)

### More

- [Contributing/Development mode](#a27)
- [Known bugs and workaround](#a21)
- [Launching the tests](#a14)
- [They talk about it](#a20)
- [License](#a15)

------

# Quick start

<a name="a1"/>
## Installation

The prefered Node version to run PM2, is the **0.11.10**

The latest stable version can always be installed via NPM :

```bash
$ npm install pm2@latest -g
```

If the above fails :

```bash
$ npm install git://github.com/Unitech/pm2#master -g
```

Common problems on installation :

- node-gyp permission problem: [Setup a new user on your server](https://github.com/Unitech/pm2/issues/188#issuecomment-30204146) or add the `--unsafe-perm` to the npm command
- if Make/GCC or other are missing `sudo apt-get install build-essential` on Ubuntu

<a name="a2"/>
## Usage

Hello world :

```bash
$ pm2 start app.js
```

<a name="a3"/>
## Examples

Raw examples :

```bash
$ pm2 start app.js --name my-api # Name process

$ pm2 start app.js -i max        # Will start maximum processes with LB depending on available CPUs

$ pm2 list               # Display all processes status

$ pm2 describe 0         # Display all informations about a specific process

$ pm2 monit              # Monitor all processes

$ pm2 logs               # Display all processes logs in streaming
$ pm2 flush              # Empty all log file
$ pm2 reloadLogs         # Reload all logs

$ pm2 stop all           # Stop all processes
$ pm2 restart all        # Restart all processes

$ pm2 reload all         # Will 0s downtime reload (for NETWORKED processes)

$ pm2 stop 0             # Stop specific process id
$ pm2 restart 0          # Restart specific process id

$ pm2 delete 0           # Will remove process from pm2 list
$ pm2 delete all         # Will remove all processes from pm2 list

$ pm2 ping               # Ensure pm2 dameon has been launched
```

## Different ways to launch a process

```bash
$ pm2 start app.js -i max  # Will start maximum processes depending on available CPUs

$ pm2 start app.js -i 3    # Will start 3 processes

$ pm2 start app.js --node-args="--debug=7001 --trace-deprecation" # --node-args command line option to pass options to node

$ pm2 start app.js -x            # Start app.js in fork mode instead of cluster
$ pm2 start app.js -x -- -a 23   # Start app.js in fork mode and pass arguments (-a 23)

$ pm2 start app.js --name serverone # Start a process an name it as server one
                                    # you can now stop the process by doing
                                    # pm2 stop serverone

$ pm2 start app.json                # Start processes with options declared in app.json
                                    # Go to chapter Multi process JSON declaration for more

$ pm2 start app.js -i max -- -a 23  # Pass arguments after -- to app.js

$ pm2 start app.js -i max -e err.log -o out.log  # Will start and generate a configuration file
```

For other nature scripts :

```bash
$ pm2 start echo.php
$ pm2 start echo.py
$ pm2 start echo.sh
$ pm2 start echo.rb
$ pm2 start echo.pl
```

<a name="a987"/>
## Options

```
Options:

    -h, --help                   output usage information
    -V, --version                output the version number
    -v --verbose                 verbose level
    -s --silent                  hide all messages
    -m --mini-list               display a compacted list without formatting
    -f --force                   force actions
    -n --name <name>             set a <name> for script
    -i --instances <number>      launch [number] instances (for networked app)(load balanced)
    -o --output <path>           specify out log file
    -e --error <path>            specify error log file
    -p --pid <pid>               specify pid file
    -x --execute-command         execute a program using fork system
    -u --user <username>         define user when generating startup script
    -c --cron <cron_pattern>     restart a running process based on a cron pattern
    -w --write                   write configuration in local folder
    --interpreter <interpreter>  the interpreter pm2 should use for executing app (bash, python...)
    --no-daemon                  run pm2 daemon in the foreground if it doesn't exist already
    --merge-logs                 merge logs
    --watch                      watch application folder for changes
    --node-args <node_args>      space delimited arguments to pass to node in cluster mode - e.g. --node-args="--debug=7001 --trace-deprecation"
```

# Features

<a name="a4"/>
## Transitional state of apps (important)

PM2 is a process manager, as said, pm2 can start, stop, restart and *delete* processes.

Start a process :

```bash
$ pm2 start app.js --name "my-api"
$ pm2 start web.js --name "web-interface"
```

Now let's say I need to stop the web-interface :

```bash
$ pm2 stop web-interface
```

As you can see **the process hasn't disapeared**. It is still there but now in `stopped` status.

To restart it just do :

```bash
$ pm2 restart web-interface
```

Now I want to **delete** the app from the pm2 process list.
To do that :

```bash
$ pm2 delete web-interface
```

<a name="a6"/>
## Process listing

![Monit](https://github.com/unitech/pm2/raw/master/pres/pm2-list.png)

To list all process running :

```bash
$ pm2 list
# Or
$ pm2 [list|ls|l|status]
```

To get more informations about a specific process :

```bash
$ pm2 describe 0
```

<a name="a7"/>
## Monitoring CPU/Memory usage

![Monit](https://github.com/unitech/pm2/raw/master/pres/pm2-monit.png)

Monitor all processes launched :

```bash
$ pm2 monit
```

<a name="a9"/>
## Logs management

### Displaying logs in realtime

![Monit](https://github.com/unitech/pm2/raw/master/pres/pm2-logs.png)

Displaying logs of specified process or all process in realtime :

```bash
$ pm2 logs
$ pm2 logs big-api
$ pm2 flush # Clear all the logs
```

### Reloading all logs (SIGUSR2/Logrotate)

To reaload all logs, you can send `SIGUSR2` to pm2 process.

You can also reload all logs via command line with :

```bash
$ pm2 reloadLogs
```

<a name="a5"/>
## Clustering

Launch `max` instances (`max` depending on the number of CPUs available) and set the load balancer to balance queries between each process :

```bash
$ pm2 start app.js --name "API" -i max
```

If your app is well designed (**state less**) you gonna be able to **process much more queries**.

Important concepts to make a NodeJS app stateless :

- Session must not be in memory but shared via a database (Redis, Mongo, whatever)
- [WebSocket/Socket.io should communicate via a database](https://github.com/LearnBoost/Socket.IO/wiki/Configuring-Socket.IO)

<a name="a690"/>
## Reloading without downtime

This special feature has been added to PM2 a while ago.

**Warning** It only works for apps in *cluster mode* (the default mode), that uses HTTP/HTTPS/Socket connections.

Reloading an app :

```bash
$ pm2 reload api
```

If the reload system hasn't managed to reload gracefully, a timeout will simply kill the process and will restart it.

### Graceful reload

Sometimes you can experience a **very long reload, or a reload that doesn't work** (fallback to restart).

It means that your app **still have open connection on exit**.

To pass this problem you have to use the graceful reload.
Graceful reload is a mecanism that will send a *shutdown* message to your process before reloading it.

Example :

```javascript
process.on('message', function(msg) {
  if (msg == 'shutdown') {
    // Your process is going to be reloaded
    // You have to close all database/socket.io/* connections

    console.log('Closing all connections...');

    // You will have 4000ms to close all connections before
    // the reload mecanism will try to do his job

    setTimeout(function() {
      console.log('Finished closing connections');
      // Ok this timeout mean that all connections have been closed
      // Now I can exit to let the reload mecanism do his job
      process.exit(0);
    }, 1500);
  }
});
```

<a name="a8"/>
## Startup script

PM2 has the amazing ability to **generate startup scripts and configure it**.
PM2 is also smart enough to **save all your process list** and to **bring back all your processes on restart**.

```bash
$ pm2 startup [ubuntu|centos|systemd]
```

**Warning** This feature is tricky to make it work generically, so once PM2 has setup your startup script, reboot your server to be sure that PM2 put back your apps !

### More informations

Two types of startup script are availables:

- SystemV init script (with the option `ubuntu` or `centos`)
- SystemD init script (with the `systemd` option)

Those options are using :

- **ubuntu** will use `updaterc.d` and the script `lib/scripts/pm2-init.sh`
- **centos** will use `chkconfig` and the script `lib/scripts/pm2-init-centos.sh`
- **systemd** will use `systemctl` and the script `lib/scripts/pm2.service`

### User permission

Ah, there is something else, let's say you want the startup script to be executed under another user.

Just use the `-u <username` option !

```bash
$ pm2 startup ubuntu -u www
```

### Derivated commands

Dump all processes status and environment managed by pm2 :
```bash
$ pm2 dump
```
It populates the file `~/.pm2/dump.pm2` by default.

To bring back the latest dump :
```bash
$ pm2 resurrect
```

<a name="a890"/>
## Watch & Restart

This feature permits to restart automatically your app when a file change in the current folder (recursively) :

```bash
$ pm2 start app.js --watch
```

<a name="a10"/>
## JSON app declaration

processes.json :

```json
[{
  "name"      : "echo",
  "script"    : "./examples/args.js",
  "args"      : "['--toto=heya coco', '-d', '1']",
  "env": {
      "NODE_ENV": "production",
      "AWESOME_SERVICE_API_TOKEN": "xxx"
  }
}
,{
    "name"       : "api",
    "script"     : "./examples/child.js",
    "instances"  : "4",
    "error_file" : "./examples/child-err.log",
    "out_file"   : "./examples/child-out.log",
    "pid_file"   : "./examples/child.pid",
    "exec_mode"  : "cluster_mode",
    "port"       : 9005
},{
  "min_uptime" : "100",
  "name"       : "auto-kill",
  "exec_mode"  : "fork_mode",
  "script"     : "./examples/killfast.js"
}]
```

Then with the cli :
```bash
$ pm2 start processes.json
$ pm2 stop processes.json
$ pm2 delete processes.json
$ pm2 restart processes.json
```

# Specific

## Specific features

<a name="a77"/>

Launching PM2 without daemonizing itself :

```bash
$ pm2 start app.js --no-daemon
```

Sending a system signal to a process :

```bash
$ pm2 sendSignal SIGUSR2 my-app
```

<a name="a989"/>
## Configuration file

You can edit these options by editing the file `~/.pm2/custom_options.sh`

These variables can be customized :

```
  DAEMON_BIND_HOST   : process.env.PM2_BIND_ADDR || 'localhost',
  DAEMON_RPC_PORT    : process.env.PM2_RPC_PORT  || 6666, // RPC commands
  DAEMON_PUB_PORT    : process.env.PM2_PUB_PORT  || 6667, // Realtime events
  DEBUG              : process.env.PM2_DEBUG || false,
  WEB_INTERFACE      : process.env.PM2_API_PORT  || 9615,
  GRACEFUL_TIMEOUT   : parseInt(process.env.PM2_GRACEFUL_TIMEOUT) || 4000,
  PM2_NODE_OPTIONS   : ''
```


## API health endpoint

```bash
$ pm2 web
```

<a name="a66"/>
## Enabling Harmony ES6

### Enable by default for all processes

You can enable Harmony ES6 by setting `PM2_NODE_OPTIONS='--harmony'` environment variable option when you start pm2 (pm2 should not be already daemonized).

To pass this option by default, you can edit `~/.pm2/custom_options.sh` and add :

```bash
export PM2_NODE_OPTIONS='--harmony'
```

Then :

```bash
$ pm2 dump
$ pm2 exit
$ pm2 resurrect
```

If ES6 has been enabled you should see this message at the beggining of each pm2 commands :

```
● ES6 mode
```

### Enable for specific processes

```bash
$ pm2 start my_app.js --node-args="--harmony"
```

<a name="a19"/>
## CoffeeScript

```bash
$ pm2 start my_app.coffee
```

That's all !

<a name="a34"/>
## Log and PID files

By default every logs (error and out), pids files, dump, pm2 logs are located in `~/.pm2/`

```
.pm2/
├── dump.pm2
├── custom_options.sh
├── pm2.log
├── pm2.pid
├── logs
└── pids
```

<a name="a89"/>
## Tutorial

[How To Use PM2 to Setup a Node.js Production Environment On An Ubuntu VPS](https://www.digitalocean.com/community/articles/how-to-use-pm2-to-setup-a-node-js-production-environment-on-an-ubuntu-vps)



<a name="a23"/>
## Execute any script: What is fork mode ?

The default mode of PM2 consists of wrapping the code of your node application into the Node Cluster module. It's called the **cluster mode**.

There is also a more classical way to execute your app, like node-forever does, called the **fork mode**.

In fork mode almost all options are the same as the cluster mode. But there is no reload or gracefulReload command.

**By using the fork mode you will lose core features of PM2 like the automatic clusterization of your code over all CPUs available and the 0s reload.**

So use it if you only need a forever-like behaviour.

Here is how to start your app within a fork:

```bash
$ pm2 start app.js -x   # Will start your app.js in fork mode
$ pm2 list              # You will see that on the row "mode" it's written "fork"
```

You can also exec scripts in other languages:

```bash
$ pm2 start my-bash-script.sh -x --interpreter bash

$ pm2 start my-python-script.py -x --interpreter python
```

<a name="a96"/>
## JSON app configuration via pipe from stdout

Pull-requests:
- [#273](https://github.com/Unitech/pm2/pull/273)
- [#279](https://github.com/Unitech/pm2/pull/279)

```bash
#!/bin/bash

read -d '' my_json <<_EOF_
[{
    "name"       : "app1",
    "script"     : "/home/projects/pm2_nodetest/app.js",
    "instances"  : "4",
    "error_file" : "./logz/child-err.log",
    "out_file"   : "./logz/child-out.log",
    "pid_file"   : "./logz/child.pid",
    "exec_mode"  : "cluster_mode",
    "port"       : 4200
}]
_EOF_

echo $my_json | pm2 start -
```

<a name="a149"/>
## Is my production server ready for PM2 ?

Just try the tests before using PM2 on your production server

```bash
$ git clone https://github.com/Unitech/pm2.git
$ cd pm2
$ npm install  # Or do NODE_ENV=development npm install if some packages are missing
$ npm test
```

If a test is broken please report us issues [here](https://github.com/Unitech/pm2/issues?state=open)
Also make sure you have all dependencies needed. For Ubuntu :

```bash
$ sudo apt-get install build-essential
# nvm is a nodejs version manager - https://github.com/creationix/nvm
$ wget -qO- https://raw.github.com/creationix/nvm/master/install.sh | sh
$ nvm install v0.11.10
$ nvm use v0.11.10
$ nvm alias default v0.11.10
```

<a name="a27"/>
## Contributing/Development mode

To hack PM2, it's pretty simple :

```bash
$ pm2 kill   # kill the current pm2
$ git clone my_pm2_fork.git
$ cd pm2/
$ DEBUG=* PM2_DEBUG=true ./bin/pm2 --no-daemon
```

Each time you edit the code be sure to kill and restart pm2 to make changes taking effect.

## Install pm2 development

```bash
$ npm install git://github.com/Unitech/pm2#development -g
```

<a name="a21"/>
## Known bugs and workarounds

First, install the lastest pm2 version :

```bash
$ npm install -g pm2@latest
```

- Node 0.10.x doesn't free script port when stopped. It's due to the NodeJS cluster module.
So if you feel that this problem is important for your use case, use the fork mode the [fork mode](#a23) instead.
By using the fork mode you will lose core features of PM2 like the automatic clusterization of your code over all CPUs available and the 0s reload.

```
$ pm2 start index.js -x  # start my app in fork mode
```

For more informations about this issue: [#74](https://github.com/Unitech/pm2/issues/74)

- `Cannot read property 'getsockname' of undefined`

When using the cluster mode (by default) you can't use ports from 0 to 1024. If you really need to exec in this range use the [fork mode](#a23) with the `-x` parameter.
By using the fork mode you will lose core features of PM2 like the automatic clusterization of your code over all CPUs available and the 0s reload.

<a name="a14"/>
## Test

```bash
npm test
```

<a name="a20"/>
## They talk about it

- http://devo.ps/blog/2013/06/26/goodbye-node-forever-hello-pm2.html
- https://coderwall.com/p/igdqyw
- http://revdancatt.com/2013/09/17/node-day-1-getting-the-server-installing-node-and-pm2/
- https://medium.com/tech-talk/e7c0b0e5ce3c

## Contributors

```
   195  tknew2
   184  Alexandre Strzelewicz
    20  Alex Kocharin
     8  soyuka
     6  sailxjx
     5  Bret Copeland
     4  AS
     4  Ville Walveranta
     4  achingbrain
     3  Ben Postlethwaite
     3  Evan You
     2  Frederico Silva
     2  Ivan Seidel
     2  MATSUU Takuto
     2  Oleg
     2  Willian
     2  Yani Iliev
     1  Almog Melamed
     1  Brent Burgoyne
     1  Daniel Pihlstrom
     1  Ed Hubbell
     1  Eugene Lucash
     1  Gil Pedersen
     1  Hao-kang Den
     1  John Hurliman
     1  Jose V. Trigueros
     1  Josh Skidmore
     1  Juozas Valenčius
     1  Kevin Gao
     1  Loïc Mahieu
     1  Mark Evans
     1  Nathan Peck
     1  TruongSinh Tran-Nguyen
     1  Wes Mason
     1  Zihua Li
     1  perfectworks
     1  subeeshcbabu
```

## Idea bucket

- Remote administration/status checking
- Builtin Inter process communication channel (message bus)
- Auto start of the script at start (upstart)
- V8 GC memory leak detection
- Web interface
- Keeping monitoring data
- Integrated wrk utils endpoint benchmark
- Scale up/down processes
- Use https://github.com/felixge/node-measured in process container

## Sponsors

Thanks to [Devo.ps](http://devo.ps/) and [Wiredcraft](http://wiredcraft.com/) for their knowledge and expertise.

[![Bitdeli Badge](https://d2weczhvl823v0.cloudfront.net/Unitech/pm2/trend.png)](https://bitdeli.com/free "Bitdeli Badge")

<a name="a15"/>
# License

Files in lib/ are made available under the terms of the GNU Affero General Public License (AGPL).
pm2-interface is made under the terms of the Apache V2 license.
