# Notes on awk


#### Structure of an AWK program:

An awk program is a sequence of pattern-action statements
```
	pattern	{ action }
	pattern	{ action }
```

***Pattern*** is a regular expression, numeric expression, string expression or combination; "action" is executable code, similar to C

Operation:
```
	for each file
		for each input line
		  for each pattern
			if pattern matches input line
				do the action
```

Usage:
	awk 'program' [ file1 file2 ... ]
	awk -f progfile [ file1 file2 ... ]

The special pattern BEGIN matches before any input has been read; the pattern END matches after all input has been read.


#### AWK features
- input is read automatically across multiple files
- lines are split into fields ($1, ..., $NF; $0 for whole line)
		  - default split is by white space
		  - changing FS to some other value (string or RE) affects split
		  - change FS by assigning to it, or by -F'...' on commandline
- variables contain string or numeric values
		  - no declarations
		  - initialized to 0 and empty string
		  - type determined by context and use: the type is set by the
		  - last operation, and might be string or number or both.  for
		  - example, x = 1 makes x a number, x = "1" makes it a string.
      
- operators work on strings or numbers
	  - coerce type according to context
	  - built-in variables for frequently-used values; see below
	  - associative arrays (arbitrary subscripts)
	  - regular expressions (like egrep)

- control flow statements similar to C
```
		if-else, while, for, do, but no switch
		for (i in array)
		   goes through each subscriptof associative array
		next: start next iteration of main loop
		exit: leave main loop, go to END block
```
- built-in and user-defined functions
    - arithmetic, string, regular expression, text edit, ...
	  - print exprlist for unformatted output.  expressions separated by
	  - current value of OFS (" " by default)
	  - printf for formatted output, as in C, to files or processes
	  - getline for input from files or processes


#### Basic AWK programs

These are all one-liners:
```
  { print NR, $0 }	precede each line by its line number
  { $1 = NR; print }	replace first field by the line number
  { print $2, $1 }	print field 2, then field 1 (and nothing else)
  { temp = $1; $1 = $2; $2 = temp; print }   flip $1, $2, print whole line
  { $2 = ""; print }	zap field 2
  { print $NF }		print last field
  NF > 0		print non-empty lines
  NF > 4		print lines with more than 4 fields
  $NF > 4		print line if last field is greater than 4
  NF > 0		{print $1, $2}	print two fields of non-empty lines
  /regexpr/		print lines that match regepxr
  $1 ~ /regexpr/	print lines where first field matches regexpr
  END { print NR }	line count: print number of records at the end
```

A couple of two-liners:
```
      { nc += length($0) + 1; nw += NF }    wc command
  END { print NR, "lines", nw, "words", nc, "characters" }

  length($1) > max { max = length($1); maxline = $0 }    print longest line
  END      { print max, maxline }

```

#### Associative arrays
AWK only provides associative arrays: subscripts are arbitrary strings or numbers.

Add up name-value pairs:
```
	    { amount[$1] += $2 }
 	END { for (name in amount)
	       print name, amount[name]
 	}
```

Test whether a[s] exists without creating it (normally, referring to
an array element creates it):
```
	if (s in a) ... 
```

To delete an element or a whole array:
```
	delete a[s]
	delete a
```

To split a string into an array:
```
	n = split(s, a, re) 
```

This splits s into a[1]..a[n] with re as delimiter.  If there is no re argument, the operation is the same as field splitting on input,
using the value of FS.


#### Built-in variables

This isn't a complete list.  Some variables can be set, others are maintained automatically (notably NR, NF).
```
	FS	input field separator; controls field splitting
	OFS	output field separator; placed between output exprs
	NF	number of fields in current record
	$1..$NF	input fields
	$0	entire input record before splitting into fields
	NR	current input record number overall
	FNR	current input record in current input file
	FILENAME current input filename
	ENVIRON	shell environment variables
	ARGV	command line arguments, origin 1; can be set
	ARGC	number of command line arguments; can be set
```

Setting ARGV[i] to "" prevents that file from being examined at all.

Fields may be set explicitly.  Assigning to $0 recomputes $1..$NF; 
assigning to $n recomputes $0.


#### Built-in functions

Awk strings and string functions are 1-origin; be careful.
```
	length(s)
		length(array) returns number of elements
	n = index(s, f)
		returns index of f in s, or 0 if not there
	n = match(s, re)
		index where re matched in s, or 0 if not
	nsub = sub(re, repl, target)
		replaces first instance of re in target by repl
		returns 0 if no match
	nsub = gsub(re, repl, target)
		replaces all instances of re in target by repl
		returns 0 if no match, number of replacements otherwise
	str = substr(s, start, length)
		returns substring of s starting at start, up to length 
		characters (default is rest of string).  works sensibly 
		if you go off the ends.  note: origin is 1.
	s = toupper(str)
	s = tolower(str)
		map case
	s = sprintf("...", exprlist)
		formats expressions, returns string result
```

There are also some of the usual math functions: int, sqrt, exp, log, sin, cos, atan2, rand (uniform between 0 and 1), srand(new_seed).


#### Functions
```
Functions are defined as 
	function name(arglist) {
		statements
	}
```

<b>statements</b> can be any sequence of statements, as in actions.
<b>return [expr]</b> returns, optionally with a value.
<b>arglist</b> is zero or more parameter names.  if there are more names than the function was called with, the extra parameters
are local variables.  (this is a *terrible* design; be careful.)
<b>arguments</b> are passed by value for scalars; arrays are in effect call by reference since the function can change the
	array contents.


#### Input and Output

Besides the automatic I/O of the main loop, 
```
	print e, e, e
		prints the list of expressions, separated by OFS
	print
		prints $0

	printf formats output, as in C
		print or printf > "string" sends output to file
		print or printf | "string" sends output to process
		given by string, which is created on first reference.

	getline x <file reads the next record into x
		returns 0 on end of file, -1 on error

	getline x | file reads from the process "file"

	flush(file) flushes the file or process

	close(file) closes the file or process (after flushing)
```

#### Gotchas

Make sure you spell variable names correctly; a misspelled name is just another variable, with a null value.  (Don't believe it?
Remove the period from the first sentence, and it's a valid awk program.)

1. To convert a string to a number for sure, add 0:  str + 0

2. To convert a number to a string for sure, concatenate "":  num ""

3. These are sometimes necessary to force the proper kind of comparison with < and other relational operators.

4. Looking at a value in an associative array creates it if it doesn't  already exist.  To check the latter, 
	if (subscript in array)

5. To delete an element,
	 ```delete array[i]```
   
6. To delete an array entirely,
	```delete array```

7. Watch out for local variables that are really global (especially loop indices like "i").  Local variables are declared as extra
parameters in the function, which is one of the worst syntax designs ever.
