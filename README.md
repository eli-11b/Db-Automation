# Get to work! Automate MS SQL Server Tasks

This tutorial will get you to work with MSSQL server automation with PowerShell.

### DISCLAIMER:

Every section will have a scenario/problem, an explanation of what a good end result or work product looks like, and links for additional information.

### Audience

MS SQL database administrators
Windows Server adminstrators
Someone who wants to learn something new (You!)

### Requirements

Open mind
Basic knowledge about what a Microsoft SQL Server Database is
Willingness to code
By the end of this you will have learned to:

### This tutorial is meant to be followed. Read the scenarios and perform the actions.

## Table of Contents

1.  Initial setup
2.  Create a variable in PowerShell
3.  Create a list in Powershell
4.  Loop / iterate through a list and do something in PowerShell
5.  Write output to html
6.  Write output to csv / excel
7.  Write output to database
8.  Create a database
9.  Drop a database
10. Backup a database
11. Introduction to splatting
12. Splatting from now on! (review 8,9,10 with splatting)
13. Restore a database
14. Restore multiple databases
15. Refresh database to lower environment
16. Search / Gather Environment error alerts
17. Strip database encryption
18. Strip database users
19. Audit Environment administrators
20. Audit: Find missing sql server backups
21. Audit: Login activity
22. Audit: Sql Server failed logins
23. Get the failed SQL Agent jobs
24. Pass parameter to script
25. PowerShell functions
26. Setup your PowerShell Profile
27. Profile: Backups
28. Profile: Restores
29. Profile: Refreshes
30. Profile: Other

<hr>

# Chapter 1

## Scenario:

You got the work laptop, time to configure so that you can automate dba tasks, write to html, write to excel or csv and more. Let's get to work!

### Initial Setup

All the commands will be typed into a PowerShell prompt.

First things first, we need to install dbatools. Dba tools is an open source PowerShell module that is going to be our powerful enabler. We are going to use the PowerShell module Install-Module to install dbatools using the CurrentUser scope meaning that it will only be on installed the current user.

#f03c15 Warning! Make sure you have permission to install modules on your machine.

Download and run "Initial-Setup.ps1" using the following command at the file's location.

```
./Initial-Setup.ps1
```

[Initial-Setup.ps1](**comingsoon** Initial-Setup.ps1)

**or to install the modules individually**

To install dbatools on your machine type in PowerShell:

```
Install-Module dbatools -scope CurrentUser
```

The "ImportExcel" module is a module that allows us to read and write to excel and csv files. To install "ImportExcel" on your machine type in PowerShell:

```
Install-Module ImportExcel -scope CurrentUser
```


The "PSWriteHTML" module is a module that will give us the ability to write to html and create custom html reports. To install "PSWriteHTML" on your machine type in PowerShell:

```
Install-Module PSWriteHTML -scope CurrentUser
```

Cool, everything should be up and ready. How do we check? To check what modules are installed type in PowerShell:

```
Get-InstalledModule
```

You should see your handy work. Lets move on.

## Explanation

Where does the module get installed when the scope is CurrentUser?

Where do modules get installed normally?

### Links

Often, the sources and links an author provides are a great source of information.

[Check out more on dbatools](www.dbatools.io)

[Check out more on PSWriteHTML](https://www.powershellgallery.com/packages/PSWriteHTML/0.0.123)

[Check out more on ImportExcel](https://github.com/dfinke/ImportExcel)


<hr>

# Chapter 2



<p><center>Create a Variable in Powershell</center></p>

<p>
Variables are a great tool in any language. It lets you represent one or multiple objects like a sql server, data, and more.
Let's check out what that looks like. </p>


I want to put my name in a variable called MyName, Type in PowerShell:

```script
$MyName = "Eli"
```

Now everytime I reference or type the variable "$MyName" it should return the value of that variable "Eli".

```script
PS> $MyName
Eli
```

Can we call a variable in the middle of a print statement?

Yes typing in PowerShell:

```script
Write-Host "Your name is $MyName"
```

Will result in this:

```script
PS> Your name is Eli
```

## Scenario:
<p>
  You are tasked with retrieving the user name from user input and storing it in the variable $UserName
</p>
Type:

```script
$UserName = Read-Host "Enter your name:"
```

## Explanation:
<p>
  Use the "Read-Host" commandlet if you want to take user input.
</p>

[For more on reading user input](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/read-host?view=powershell-7.1)


This will be useful for later on because we can store just about anything we want in a variable.

# Chapter 3
<p>
    For the purposes of this tutorial the terms Lists and Arrays are used interchangebly. Lists let the user store multiple values under one variable. 
</p>

## Scenario 1:
Create a grocery list containing: apples, oranges, cereal, and milk under the name $GroceryList
<br>type:
```script
$GroceryList = "apples", "oranges", "cereal", "milk"
```

## Explanation:

Typing "$GroceryList" will bring up all the values in the array. <br>
To select the first value from the array the user can use: $GroceryList[0]

## Scenario 2:
<p>
    You need to create a list of Sql Servers that represent the environment you intend to work in. Call this list $DevEnvironment.
</p>

```script
$DevEnvironment = "Server01", "Server02", "192.168.1.100"
```
## Explanation:
    You can input FQDN, IP addresses or IP addresses with custom port ("192.168.1.100,1400") for a SQL Server Instance.


[For more on Arrays/Lists](https://adamtheautomator.com/powershell-arraylist/)

# Chapter 4
### Loops!!!
<p>
    Loops are a powerfull concept in programming.Let's learn a <strong>foreach</strong> loop.
    The foreach loops we will use have this pattern: 
</p>

foreach loop pattern:
```script
foreach(item in list){
    do something
}
```

## Scenario 1:
<p>
    Whats on the grocery list? Create a loop that prints all the items in the grocery list. 
</p>

type:
```script
#Create the grocery list
$GroceryList = "apples", "oranges", "cereal", "milk"


#Loop through each item in the grocery list and print them.
foreach($item in $GroceryList){
    Write-Host $item
}
```


## Explanation:
<p>
    The loop creates a variable called $item (you can name it whatever you want) on the fly that represents each item in the grocery list as it loops through. 
    Meaning, the $item variable will come to represent apple, oranges, cereal, and milk as it goes through the $GroceryList. It will then print out the value of the $item variable at that moment in the loop. So it will go through and print everything in the $GroceryList. 
</p>
    
## Scenario 2:
<p>
    You have to work on some databases. The names of the databases (Server01, Server02, Server03) are in a list you will create named $OriginalDBs. To make sure that you are not making changes on the original databases themselves you need to create another list $CopyDBs that will house the name of the orginal databases you plan to work on, but you are going to append "_COPY" on the names so that it is clear they are copies. 
</p>

To create the OriginalDBs list:
```script
$OriginalDBs = "Server01", "Server02", "Server03"
```

To create the new list $CopyDBs using values from within $OriginalDBs and appending "_COPY" to the values with a "foreach loop" 
```script
foreach($db in $OriginalDBs)
    $db = $db +"_COPY"
    $CopyDBs =+ $db
```

Checkout our new list:
```script
foreach($NewDB in $CopyDBs){
    Write-Host $NewDB
    }
``` 
## Explanation:
<p>
    We create a loop that goes through each item in the $OriginalDBs list. In the loop we replace the value inside the variable $db with a new value. The new value is the original value that gets passed into the variable $db (each item in the $OriginalDBs list) and appends "_COPY" to it. The next part "$CopyDBs =+ $db" appends or adds the newly created $db values with the "_COPY" to a list called $CopyDBs. We then check our handy work by typing $CopyDBs or the loop above to go through and print each item. 
</p>
 
 # Chapter 5
 **Write output to HTML**

 You will need to create reports, the one way is creating  spreadsheets. What about a custom html report?

 ## Scenario 1:

 <p>
    We need to create an html report of the current running SQL services on a target SQL Server.
 </p>


 ```script
 Get-Service |Where{Name -Match "SQL"}|ConvertTo-HTML|Out-File "//Networkshare/reports/SqlService.html"
```
## Explanation

<p>
    We need to run the commandlet "Get-Service" then filter the results to only show the ones that have SQL in the name. We convert this output to html and drop it in a file on the networkshare under the reports directory called SqlService.html
</p>

## Multiple Servers as a target
```script

#Target Servers
$Servers  = "Server01", "Server02", "Server03"

foreach($Server in $Servers){
    $ReportName  = $Server + ".html"
    Get-Service|Where{Name -Match "SQL"}|ConvertTo-HTML|Out-File "//Networkshare/reports/$ReportName"
}
```

## Explanation
<p>
    To create a similar report that target multiple servers, create a variable that will store the targets. Loop through and append the name of the server and ".html" and write the report to the network share. 
</p>

## Scenario 2:
<p>
    We need the same information, but don't need to save it to a file. Show it to me on a web page. 
</p>

```script
Get-Service|Where{Name -Match "SQL"}|Out-HTMLView
```
## Explanation

<p>
    This will create a open a new tab containing the information on your default web browser. 
</p>

## Scenario 3:
<p>
    We need to clean up our reports. It contains information that we are not using and distracts from the objective of the report. This means reports that are written to html and reports that we make pop up in a browser. We have to select the <strong>properties</strong> that we want to keep from the report. 
</p>

Let's see what that looks like.
