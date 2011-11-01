# Using the Play 2.0 console

## Launching the console

The Play 2.0 console is a development console based on **sbt** that allow to manage the complete development cycle of a Play application.

To launche the console, enter any existing application directory and launch the `play` script:

```bash
$ cd /path/to/any/application
$ play 
```

[[console.png]]

## Getting help

Use the `help` command to get basic help about the available commands:

```bash
[My first application] $ help

Welcome to Play 2.0!

These commands are available:
-----------------------------
classpath                  Display the project classpath.
clean                      Clean all generated files.
compile                    Compile the current application.
console                    Launch the interactive Scala console (use :quit to exit).
dependencies               Display the dependencies summary.
dist                       Construct standalone application package.
exit                       Exit the console.
h2-browser                 Launch the H2 Web browser.
license                    Display licensing informations.
package                    Package your application as a JAR.
publish                    Publish your application in a remote repository.
publish-local              Publish your application in the local repository.
reload                     Reload the current application build file.
run                        Run the current application in DEV mode.
start                      Start the current application in another JVM in PROD mode.
update                     Update application dependencies.

You can also use any sbt feature:
---------------------------------
about                      Displays basic information about sbt and the build.
reboot [full]              Reboots sbt and then executes the remaining commands.
< file*                    Reads command lines from the provided files.
!!                         Execute the last command again
!:                         Show all previous commands
!:n                        Show the last n commands
!n                         Execute the command with index n, as shown by the !: command
!-n                        Execute the nth command before this one
!string                    Execute the most recent command starting with 'string'
!?string                   Execute the most recent command containing 'string'
~ <action>                 Executes the specified command whenever source files change.
projects                   Displays the names of available projects.
project [project]          Displays the current project or changes to the provided `project`.
- command                  Registers 'command' to run if a command fails.
iflast command             If there are no more commands after this one, 'command' is run.
( ; command )+             Runs the provided semicolon-separated commands.
set <setting-expression>   Evaluates the given Setting and applies to the current project.
tasks                      Displays the tasks defined for the current project.
inspect <key>              Prints the value for 'key', the defining scope, delegates, related definitions, and dependencies.
eval <expression>          Evaluates the given Scala expression and prints the result and type.
alias                      Adds, removes, or prints command aliases.
append command             Appends `command` to list of commands to run.
last <key>                 Prints the last output associated with 'key'.
last-grep <pattern> <key>  Shows lines from the last output for 'key' that match 'pattern'.
session ...                Manipulates session settings.  For details, run 'help session'..

Browse the complete documentation at http://www.playframework.org.

```
