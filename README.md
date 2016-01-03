# github webhooks

Execute shell commands on the server as a result of a github webhook event.

## Server command

```
github-webhooks server -configuration=github-webhooks.json
```

### Configuration file example

Note that **host** and **port** are optional (defaults are 0.0.0.0 and 3091) and that if you want to **target hooks
to a specific branch** you have to add another node to the configuration specifying the
branch name after the repository name, for example if you want to accept hooks
from `fntlnz/dockerfiles` master branch you have to use `fntlnz/dockerfiles/master` as repo name.

As you have probably noted each repository have a node called events,
here you have to specify the event to listen (for example `push`) and on each event a list of commands to execute.

The **path** node allows you to overwrite the `$PATH` environment variable,
if not set default `$PATH` is used.

```json
{
    "host": "0.0.0.0",
    "port": "3091",
    "path": "/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin",
    "repositories": {
        "fntlnz/dockerfiles": {
            "events": {
                "ping": ["touch ping-on-any-branch.txt"]
            }
        },
        "fntlnz/dockerfiles/master": {
            "events": {
                "push": ["touch push-on-master-branch.txt"]
            }
        }
    }
}
```


## Installation

### From source

```
go get github.com/fntlnz/github-webhooks
```

### Pre-built binaries

Them are coming soon!

## Security

It's recommended to configure your firewall to accept only requests coming
from trusted servers. In this case trusted servers are the GitHub ones.

To know which are ip addresses of GitHub hooks servers take a look at [https://api.github.com/meta](https://api.github.com/meta)

### iptables

```bash
# Create a chain named GitHubWebHooks
iptables -N GitHubWebHooks
 
# Enable the 3091 port on the chain

iptables -I INPUT -s 0/0 -p tcp --dport 3091 -j GitHubWebHooks

# Enable trusted ips on GitHubWebHooks  chain 

iptables -I GitHubWebHooks -s 192.30.252.0/22 -j ACCEPT
 
# Drop the public from GitHubWebHooks chain
iptables -A GitHubWebHooks -s 0/0 -j DROP

# Save
service iptables save

# Restart
service iptables restart

```
