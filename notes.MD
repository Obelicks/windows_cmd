# Introduction to Windows Command Line
The scope of this module is how to navigate and administer a windows host from the commandline. These notes a written from a penetration testers perspective so the focus will be on reconnaissance, exploitation and exfiltration of data.

## Cheat Sheet 
Useful commands will be listed in this table for the ease of use. 

### CMD
| Command | Purpose |
| ------- | --------| 
| dir | print directory |
| help  | displays a list of built-ins  |
| help <commandName> |dispaly helpul information about the <commandName> |
| cls | clears the command prompt |
| systeminfo | prints information about the system | 
| ipconfig | shows networking information |
| doskey /history | prints a history of used commands | 
| cd | prints current working directory |

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

### Basic Usage
Use the command `dir` to look around the the current directory, which should be denoted `C:\Users\<userName>`.   

#### Windows Recovery Trick
When booting from a windows installation disk we can initialize repair mode which launches a command prompt. From here we can overwrite the `sethc.exe` executeable with the `cmd.exe` now when we are at the login  screen we can press **shift** 5 times to open a command prompt with NT 

### Getting Help
When having trobule in the Command Prompt the `help` command is a useful tool with an argument is prints a list of system built-ins commands and gives a short despriction of their purpose. If you call the help command with another built-ins command as the argument it will display helpful information about the built-in command.

Some commands dont have a detailed section for the `help` command, but a reference to their own like `ipconfig` 
```
> help ipconfig

This command is not supported by the help utility. Try "ipconfig /?".
```

### History 
Come on, don't look at a mans CLI history... Well if you have to then use `doskey /history`
It will print a newline terminated list with previous commands regardless of the commands where valid or not. 

#### shortcuts
| key | description | 
| --- | ----------- |
| page up | Place the first command in the history | 
| page down | place the last command in the history | 
| up arrow | scroll one command back in the hisotry | 
| down arrow | scroll one command forward in the history |
| right arrow | Types the previous command one char at a time |
| F3 | retype previous command | 
| F5 | cycle through previous commands | 
| F7 | Opens an interactive list of previous commands |
| F9 | Enter a command to our prompt based on number, number corrosponds to commands in our history |

### Exit a Process
like with linux **ctrl + c** will abort a running process  

## System Navigation
