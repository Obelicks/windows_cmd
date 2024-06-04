# Powershell cmdlets

``` 
> $PSVersionTable
Name                           Value
 ----                           -----
 PSVersion                      7.3.6
 PSEdition                      Core
 GitCommitId                    7.3.6
 OS                             Linux 5.4.0-1058-azure #60~18.04.1-Ubuntu SMP Tue Aug 31 20:34:4…
 Platform                       Unix
 PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0…}
 PSRemotingProtocolVersion      2.3
 SerializationVersion           1.1.0.1
 WSManStackVersion              3.0
 ```

This returns a big object. If we are only interested in one of the attributes we could call it like:

 ```
 $PSVersionTable.PSVersion

Major  Minor  Patch  PreReleaseLabel BuildLabel
-----  -----  -----  --------------- ----------
7      3      6  
```

## Locate Commands 
a cmdlet is named with the verb-noun convention like `Get-Service`

### Locate with Get-Command
`Get-Command -Noun alias*`
This will list all command where the noun starts with alias.
### Get Help
`Get-Help` is a great way off... getting help with a command, but did you know you can use -Examples to get an example of how to use the command and what to expect of the output.

`Get-Help Get-FileHash -Examples`


