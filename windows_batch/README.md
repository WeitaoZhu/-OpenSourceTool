## [fart工具主页](https://netactuate.dl.sourceforge.net/project/fart-it/fart-it/1.99b/fart199b_win32.zip)

## [fart 命令手册](http://fart-it.sourceforge.net/)

```bash

Usage: FART [options] [--] <wildcard>[,...] [find_string] [replace_string]

Options:
 -h --help          Show this help message (ignores other options)
 -q --quiet         Suppress output to stdio / stderr
 -V --verbose       Show more information
 -r --recursive     Process sub-folders recursively
 -c --count         Only show filenames, match counts and totals
 -i --ignore-case   Case insensitive text comparison
 -v --invert        Print lines NOT containing the find string
 -n --line-number   Print line number before each line (1-based)
 -w --word          Match whole word (uses C syntax, like grep)
 -f --filename      Find (and replace) filename instead of contents
 -B --binary        Also search (and replace) in binary files (CAUTION)
 -C --c-style       Allow C-style extended characters (\xFF\0\t\n\r\\ etc.)
    --cvs           Skip cvs dirs; execute "cvs edit" before changing files
 -a --adapt         Adapt the case of replace_string to found string
 -b --backup        Make a backup of each changed file
 -p --preview       Do not change the files but print the changes

```
- 举例:
如果要替換`&A` ,則需要使用引號以確保`&`不會被shell解釋

```powershell
fart in.txt "&A" "B" 
```
