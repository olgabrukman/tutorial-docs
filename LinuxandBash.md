[Linux App File Sytem Structure](https://en.wikipedia.org/wiki/Filesystem_Hierarchy_Standard)
[Bash Guide](http://tldp.org/LDP/Bash-Beginners-Guide/html/index.html)

## Linux Tricks
* Clearing your terminal **ctrl-l**
* Move to beginning or end of line **ctrl-a, ctrl-e**
* Move forward or back one word **alt-f, alt-b**
* Searching through shell history **ctrl-r**
* Re-run previous command with 'sudo' prepended **sudo !!**
* Cutting and pasting text in the command line **ctrl-k, ctrl-u, ctrl-w, ctrl-y**
* Use **less +F'** to view logfiles, instead of **tail**. Use **ctrl-c, shift-f, q to quit** to navigate the open file.
* Continue editing your current shell line in a text editor (uses $EDITOR) **ctrl-x-e**
* Paste previous command's argument (useful for running multiple commands on the same resource) **alt-.**
* Resets/unborks your terminal **reset**


## LSOF (List Of Files)
* **lsof** - list of open files
* **lsof [filename]** -process id that has the file open
* **lsof -p [pid]** - list of file the process has open
  * **lsof -p [pid]|grep bin** - the process binary
  * **lsof -p [pid]|grep .log** - the process log file
  * **lsof -p [pid]|grep .so** - list of the process open shared object files
* **lsof -u [username]** - list of file used by the user
* **lsof -i :80** - which process(es) is listening on port 80
* **lsof -i tcp** - which process(es) is listening on protocol tcp


http://langevin.univ-tln.fr/cours/UPS/extra/Linux-101-Hacks.pdf
