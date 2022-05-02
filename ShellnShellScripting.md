# Bourne Shell & Shell Scripting
This note will explore **Bourne Shell** and **Shell Scripting** for RHEL RHCSA. 

## RHCSA Objective 
1. Use input-output redirection (>,>>,|,2>, etc)
2. Use grep and regular expression to analyze text

### Shell & Environment Variables
A _variable_ is a transient storage for data in memory. It retains information that is used for customizing the shell environment and referenced by many programs to function properly. The shell stores a value in a variable, and one or more white space characters must be enclosed within quotation marks ("").

There are two types of variables: _local_ (or _shell_) and _environment_. A local variable is private to the shell in which it is created, and it's value cannot be used by programs that are not started in that shell. This introduces concept of _current_shell and _sub_-shell (or _child_ shell. The current shell is where a program is executed, whereas a sub-shell (or child shell) is created within a shell to run a program. The value of local variable is only available in the current shell.

The value of an environment variable is inherited from the current shell to the sub-shell during the execution of a program. In other words, the value stored in an environment variable is accessible to the program, as well as any sub-programs that it spawns during its lifecycle. Any environment variable set in a sub-shell is lost when the sub-shell terminates.

There are a multitude of predefined environment variables that are set of each user upon logging in. Use the **env** or the **printenv** command to view their values. Run these commands on localhost as normal user and observe the output. there should be around 25 of them. Some of the common predefined environment variables are described in following table.

**Variable** | **Description**
-------------|----------------
DISPLAY | Stores the hostname or IP address for graphical terminal sessions
HISTFILE | Defines the file for storing the history of executed commands
HISTSIZE | Defines the maximum size for the HISTFILE
HOME | Sets the home directory path
LOGNAME | Retains the login name
MAIL | Contains the path to the user mail directory
PATH | Defines a colon-separated list of directories to be searched when executing a command. A correct setting of this variable eliminates the need to specify the absolute path of a command to run it
PPID | Holds the identifier number for the parent program
PS1 | Defines the primary command prompt
PS2 | Defines the secondary command prompt
PWD | Stores the current directory location
SHELL | Holds the absolute path to the primary shell file
TERM | Holds the terminal type value
UID | Holds the logged-in user's UID
USER | Retains the name of the logged-in user

RHEL provides the _echo_ command to view the values stored in variables. For instance, to view the value for the PATH variable, run the _echo_ command and ensure to prepend the variable (PATH) with the dollar sign ($):
```shell
echo $PATH
```
