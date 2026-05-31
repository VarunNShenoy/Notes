Get-Content : Retrieves (gets) the content of a file and displays it in the console.
Set-Location : Changes the Current Working Directory

Get-Command: List of all cmdlets, fucntions, aliases and scripts.

For each CommandInfo object retrieved by the cmdlet, some essential information (properties) is displayed on the console. It’s possible to filter the list of commands based on displayed property values. For example, if we want to display only the available commands of type “function”, we can use -CommandType "Function", as shown below:

Get-Help : It provides detailed information about cmdlets, including usage, parameters, and examples. It’s the go-to cmdlet for learning how to use PowerShell commands.

Powershell includes aliases - which are shortcuts or alternative names for cmdlets.

Get-Alias : Lists of aliases available. dir is an alias for Get-ChildItem, and cd is an alias for Set-Location.

Another powerful feature of PowerShell is the possibility of extending its functionality by downloading additional cmdlets from online repositories.

To search for modules (collections of cmdlets) in online repositories like the PowerShell Gallery, we can use Find-Module. Sometimes, if we don’t know the exact name of the module, it can be useful to search for modules with a similar name. We can achieve this by filtering the Name property and appending a wildcard (*) to the module’s partial name, using the following standard PowerShell syntax: Cmdlet -Property "pattern*".

Once identified, the modules can be downloaded and installed from the repository with Install-Module, making new cmdlets contained in the module available for use.

**Navigating the file systems and working with the files**

dir/ls - Get-ChildItem
Cd - Set-Location
Create Item - New-Item , need to specify the type of item by giving  -ItemType "File" or "Directory"
Remove Item - Remove-Item.
Copy-Item -Source Path -Destination 







