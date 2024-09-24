---
title: "Common linux and git commands"
# date: 2024-09-13T01:43:12+05:30
draft: false
tags: ["Linux", "Git"]
weight: 3
---

Some common linux commands are -

- **ls** - show current files and directories
- **ls -l** - show files and directories with permissions
- **cd** - change directory
- **hostname** - shows the host device name
- **ifconfig** - display IP and MAC address
- **top** - show all running processes
- **cat filename** - shows the contents of the "filename"
- **mkdir** - create directories
- **rm -rf dirname** - removes directory and inside files. -r is for recursive and -f is for force.
- **history** - see the history of all the commands.
- **ping host-ip** - Check connectivity between hosts
- **chmod 777 filename** - Assign read, write and execute permissions(rwx) to owner, group and others.
- **chmod -x filename** - **Remove** execute functionality of file.
- **chown** - change owner and group ownership of file
- **netstat -tulpn** - check open ports which are **listening** of **tcp** and **udp** with their **pid**.
- **tcp dump port_number** - check packets incoming and outgoing to the given port number
- **alias** command to create a command alias such as `alias lh="ls -lrth"` and so later, when u do `lh`, it runs same as `ls -lrth`
- **brctl show** -Shows information about the **bridge network and veth interfaces it is connected to** and its attached ports of **veth pair** in kubernetes
- **route**- check the route table
- **ip addr** - Displays the addresses for every link configured on the system, same as `ip a`
- **ip link sh** - Inspect the network configuration within pod
- **ethtool -S eth0 or veth12abcd** - query or control network driver and hardware settings with statistics flag
- **ip route sh** - Show route table, same as `route -n`
- **ping ip** - shows connectivity to the IP(cannot use port)
- **nc -vz ip_addr port** - Checks tcp connectivity `nc -vz 10.99.52.54 30080`
- **traceroute** - Shows route of packet flow `traceroute ip`
- **w or sudo w** - check current logged in session for terminal
- **pv** - monitor progress of a command execution `pv file.tar.gz > /tmp/file.tar.gz`
- **htop** - see current cpu utilization
- **free -kh** - see memory usage
- **du -sh** - see disk utilisation

## Git commands

`git rev-parse --short HEAD` - get the latest commitId

`git remote -v` - check repo which u are on

`git branch` - check current branch

`git branch -r` => remote branch use `| wc -l` to count branches

`git checkout -b branchName` - switch branches

`git fetch` - fetch all branches **without merging anything**
Creating a local branch B from remote origin/B and checkout to it via -
`git checkout -b B origin/B`
then do `git pull`

`git switch` - For remote branches switch, you can run `git switch branchName` as well

---

#### Linux file permissions

**User** is the **owner** which creates the file. The user which creates is the **owner by default**.
**Group** is group of multple users and all users of same group have same permissions alloted.
**Other** is anyone who has access to file and is **not** owner and user.

We change owner of file using **chown** like `chown owner_name filename`.

Permissions are represented by numbers in following format -

- 4 => read
- 2 => write
- 1 => execute

To give exact permission of read to all (user, group and others), command is `chmod 444 filename`
The above method gives exact permission to file for all types of users.

For **owner** to have read and write (4+2), **group** to have execute(1) and **other** to have write and execute(2+1), the command is `chmod 613 filename`
