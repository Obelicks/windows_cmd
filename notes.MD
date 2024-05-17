# Introduction to Windows Command Line
The scope of this module is how to navigate and administer a windows host from the commandline. These notes a written from a penetration testers perspective so the focus will be on reconnaissance, exploitation and exfiltration of data.

## Cheat Sheet 
Useful commands will be listed in this table for the ease of use. 

| Command | Purpose |
| ------- | --------| 

## Command Prompt vs Powershell
### Command Prompt
The command prompt was released in 1981 and have a lot of limitation compared to its younger brother "Powershell". Command Prompt does for example not support aliases for its command, it cannot pass command output to a new command like "|" in bash. 
Commands has to be run one at a time. 
* Output is pure text

### Powershell 
* Can use command aliases.
* cmdlet output can be passed to another cmdlet
* All output is in the form of an object
* Can execute a sequence of commands
* Has an integrated Scripting Enviroment 
* Works on Linux


## Command Prompt Basics
The command prompt binary is called cmd.exe or CMD.exe windows have a casual attitude towards capitalized letters. It is based on the COMMAND.COM interpreter in DOS.

### Access CMD
windows key + r type `cmd`
executing the executable from its path:
`C:\Windows\System32\cmd.exe`

