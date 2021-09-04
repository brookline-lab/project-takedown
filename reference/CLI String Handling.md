---
Title: String Manipulation on the Command Line
Summary: This document will cover two very different methods of piecing together scattered data
Environments: *nix, PowerShell
Category: Reference
---


A file `flag.txt`has the following contents:
```sh
#I say FLA
#you say G{str@
#we say cli}
#
```

The goal is to take all the flag pieces from the line endings and put them into one string: **FLAG{str@CLI}**, while also showcasing several tools along the way.  The solutions here are not the most efficient but serve as a reminder of what is available to the system.

## ***nix**
```sh
head -3 | rev | cut -d " " -f1 | rev | tr -d "\n"
```
Since the fourth line does not contain any needed data the first objective is to exclute it.  The command `head -3` will output only the first 3 lines.

Reversing the lines with `rev` will make it easier to trim off the remaining unnecessary parts.

The next command, `cut` , is called with a whitespace delimiter and leaves only the 1st field, which is all the data up until the delimiter.

At this point the data can be reversed back to the original order.

The final step is to delete the newline characters using `tr`
## PowerShell

```powershell
$file = Get-Content .\flag.txt | Select -First 3; foreach ($x in $file) { $x = $x.Split(" "); $out += $x[$x.Count -1] }; echo $out
```

The PowerShell solution needs to be quite different due to the nature of the environment. This is a bit difficult to read on one line, so a more detailed breakdown is below:

```PowerShell
# Store the first 3 lines of the file in a variable
$file = Get-Content .\flag.txt | Select -First 3

# $file is now an array, with each element being a line of text
foreach ($x in $file) {
	# $file will be further split into $x
	# $x is a temporary array containing the individual words in
	# each line.
	$x = $x.Split(" ")
  # $out will store the last word of each line
	$out += $x[$x.Count -1] 
}
echo $out
```


