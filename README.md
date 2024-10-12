# popo

The "poisoner poisoner." A fork of respounder that passes honeycreds to responders and other LLMNR poisoners.


## Download

### Latest Releases
Popo is available for 64-bit Linux. More versions will come later.
Latest versions can be downloaded from the
[Release](https://github.com/io-project-cyber/popo/releases) tab above.

### Build from source
This is a golang project with ~~no~~ one dependency. Sorry, respounder.

```
sudo apt update
sudo apt install git golang

#Get our repository
git clone https://github.com/io-project-cyber/popo.git
cd ./popo

#Download the library we need (zgrab2)
go mod download
```

**READ BEFORE YOU BUILD**

At this point, we need to replace one of the files (smb.go) in the library. It doesn't like working with incomplete sessions.

The zgrab2 library should be in your $GOROOT or $GOPATH, but during testing, downloading without those variables set was pretty inconsistent, so I don't feel like a script would be reliable.

You're looking for a file path which looks something this: .../go/pkg/mod/github.com/stacktitan/smb@v0.0.0._____/smb/smb.go

Make sure to replace it with the smb.go included in this repository.

Once you've done these steps, the executable is ready to be built.
```
go build popo.go
```

## Usage

Running `popo` is as simple as invoking it on the command line.
Example invocation:
```bash
$ ./popo
	______   ____ ______   ____  
	\____ \ /  _ \\____ \ /  _ \ 
	|  |_> >  <_> )  |_> >  <_> )
	|   __/ \____/|   __/ \____/ 
	|__|          |__|           

[ens33]    Sending probe from 192.168.1.119...	responder detected at 192.168.1.160
Sending honeycreds to 192.168.1.160
2024/10/11 21:42:15 Success!
```

### Flags

```
$ ./popo [-json] [-debug] [-hostname testhostname | -rhostname]

Flags:
  -json
        Prints a JSON to STDOUT if a responder is detected on
        the network. Other text is sent to STDERR
  -debug
        Creates a debug.log file with a trace of the program
  -interface string
        Interface where responder will be searched (eg. eth0).
        Not specifying this flag will search on all interfaces.
  -hostname string
        Hostname to search for (default "aweirdcomputername")
  -rhostname
        Searches for a hostname comprised of random string instead
        of the default hostname ("aweirdcomputername")
```

### Typical usage scenario

#### Personal
Detect rogue hosts running responder on public Wi-Fi networks
e.g. like airports, cafés and avoid joining such networks
(especially if you are running windows OS)

#### Corporate
Detect network compromises as soon as they happen by running respounder
in a loop

For eg. the following `crontab` runs respounder every minute and logs a JSON file to syslog
whenever a responder is detected.
```bash
* * * * * /path/to/popo -json | /usr/bin/logger -t responder-detected
```

Example `syslog` entry:
```bash
code@express:~/$ sudo tail -f /var/log/syslog
Feb  9 03:44:07 responder-detected: [{"interface":"vmnet8","responderIP":"172.16.55.128","sourceIP":"172.16.55.1"}]
```

## Future plans
Sure, we can pass honeycreds. But how do we track them? How can we tell all of our machines that something is a honeycred and raise maximum alert if it's seen?
