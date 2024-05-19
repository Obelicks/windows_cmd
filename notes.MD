# Introduction to Windows Command Line
The scope of this module is how to navigate and administer a windows host from the commandline. These notes a written from a penetration testers perspective so the focus will be on reconnaissance, exploitation and exfiltration of data.

While tools exits to make navigating on windows machines easier for linux users, these tools usually leave traces on the host machine of our presence. What we instead can do is to "live of the land" which means using build-in commands and functionality to do our work, instead of relying on outside tools that may expose us.

## Cheat Sheet 
Useful commands will be listed in this table for the ease of use. 

### CMD
| Command | Purpose |
| ------- | --------| 
| dir | print directory |
| help  | displays a list of built-ins  |
| help \<commandName> |dispaly helpul information about the <commandName> |
| cls | clears the command prompt |
| systeminfo | prints information about the system | 
| ipconfig | shows networking information |
| doskey /history | prints a history of used commands | 
| cd | prints current working directory |
| cd \<dir>| change directory to the dir in argument | 
| tree | explore the file system tree with your current working directory as the root | 
| md or mkdir | creates a subdir in the current working directory with the name given in argument 1 |
| rd or rmdir | does the opposite of md and mkdir, it removes the directory specified in argument 1 |
| find "string" | like grep on Linux |
| del or erase | deletion tool | 

## Command Prompt vs Powershell
### Command Prompt
The command prompt was released in 1981 and have a lot of limitation compared to its younger brother "Powershell". Command Prompt does for example not support aliases for its command, it cannot pass command output to a new command.
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
we can use `cd` or `chdir` with arguments to print our current working directory.
To change directory we can add the directory we want to move to, to the 1st argument of `cd` to move there. 

Note that `C:\` is the root directory of a Windows machine. Historically drive A and B were for floppy disk so the thirds drive C was the harddrive. On modern computers there are no longer floppy disk slots but C:\ being the root of the windows system is still the case for legacy reasons.

Like Linux, Windows can also use relative and absolute pathing. Remember to note what drive you are in when using absolute paths. 

Using the `tree` command we can print a comprehensive view of the filesystem tree with our current working directory as the root of the tree.
with the `/F` parameter we can also get the files in the folders with the `tree` command

### Interesting Directories

| Name: |	Location:¨|	Description:|
|--|---|---
%SYSTEMROOT%\Temp |	C:\Windows\Temp |	Global directory containing temporary system files accessible to all users on the system. All users, regardless of authority, are provided full read, write, and execute permissions in this directory. Useful for dropping files as a low-privilege user on the system. |
|%TEMP% |	C:\Users\<user>\AppData\Local\Temp |	Local directory containing a user's temporary files accessible only to the user account that it is attached to. Provides full ownership to the user that owns this folder. Useful when the attacker gains control of a local/domain joined user account.|
| %PUBLIC% |	C:\Users\Public |	Publicly accessible directory allowing any interactive logon account full access to read, write, modify, execute, etc., files and subfolders within the directory. Alternative to the global Windows Temp Directory as it's less likely to be monitored for suspicious activity. |
|%ProgramFiles% |	C:\Program Files |	folder containing all 64-bit applications installed on the system. Useful for seeing what kind of applications are installed on the target system. |
|%ProgramFiles(x86)% |	C:\Program Files (x86) |	Folder containing all 32-bit applications installed on the system. Useful for seeing what kind of applications are installed on the target system.|


### Managing Directories
With the command `md <dirName>` we can create a directory. `md` is short for `mkdir` which performs the same operation.

we can remove directories with the `rd` or `rmdir` commands.

Trying to remove a non-empty directory will result in failure however. Unless given the paramter `/S`. 

#### Modify
Directories holds data, other directories, and files which also holds data. Therefore modifying them is a complex task that we let commands do for us:
* Move 
* Robocopy
* xcopy

`move <source> <destination>` like its Linux cousin `mv` we input 1st what we want to move, then as 2nd argument where we want to move it to.

`xcopy <source> <destination>` Like the name implies this command copies the source file to the destination. If given a directory xcopy will copy files and subdirectories however not empty subdirectories unless `/E` is given. xcopy will reset file and dir attributes unles the `/K` switch is passed.

`robocopy`is the successor of xcopy with more options and utilities. 
robocopy can move accross drives and networks while stile maintaining file data, attributes, ownership, ACLs, and any flags set like hidden or read-only.

If a user does not have permission to copy files we can use the `/MIR` switch, this will however mark the files as backup and hide them, which can be prevented with the `/A-:SH`(attribute show hidden). If you dont want to commit to the copy you can use the `/L` to see a preview of the operation without it executing, a good idea if you are not 100% certain of what you are doing. 

example:
`robocopy /E /MIR /A-:SH C:\Users\htb\Desktop\notes\ C:\Users\htb\Documents\Backup\Files-to-exfil\`

### Files
The `more` command displays the content of a file starting from the head until the terminal cannot show more, you can scroll further in the file with enter. Use the switch `/S` to crunch blanks spaces down to a single line.

outputs can be piped to more with the `|` symbol `ipconfig | more`

`openfiles` is an admin command that lets us see what files the users on our host has opened

`type` can display the contents of multiple text files at once. It uses a non locking operation to do so. 

`type` can also direct its output to other files `type secrets.txt >> exposed.txt`

#### Create and modify 
`echo` can be used to print something to the terminal and like on Linux can be used to write and append to a file 

`echo something > text.txt` 

`echo more things >> text.txt`

`fsutil` filesystem utility lets us create a file. 

`fsutil createNew for-sure.txt 222`

`ren` ren(ame) allows the user to rename a file (arg1) to something else (arg2)

#### Input / Output

**<, >, |, &** can be used to direct input and output to and from commands. 

**&&** can be used between two statemenets to run statement a and if it succeeds run b

**||** can be used similiarly but statement b only gets executed if a fails. 

#### Deleting Files
For deletion we can use `del` which also has the alias `erase`.  
```
> help erase
Deletes one or more files.

DEL [/P] [/F] [/S] [/Q] [/A[[:]attributes]] names
ERASE [/P] [/F] [/S] [/Q] [/A[[:]attributes]] names
  /P            Prompts for confirmation before deleting each file.
  /F            Force deleting of read-only files.
  /S            Delete specified files from all subdirectories.
  /Q            Quiet mode, do not ask if ok to delete on global wildcard
  /A            Selects files to delete based on attributes
  attributes    R  Read-only files            S  System files
                H  Hidden files               A  Files ready for archiving
                I  Not content indexed Files  L  Reparse Points
                O  Offline files              -  Prefix meaning not
```

#### Copying and moving files
cope and move can be used for these desired operations.
use /v to increase verbosity given you validation after the operation.

## System Information
Now that we have found our feet on a windows host it is time to **Gather System Information.**

In *any* offensive scenario it is important to enumerate the systems which are targeted. This can be done from outside but certainly also from the inside.

What kind of information could be interesting?

* General System Information
* Networking Information
* Basic Domain Information
* User Information

