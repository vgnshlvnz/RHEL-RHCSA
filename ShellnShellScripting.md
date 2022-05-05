# Bourne Shell & Shell Scripting
This note will explore **Bourne Shell** and **Shell Scripting** for RHEL RHCSA. 

## RHCSA Objective 
1. Use input-output redirection (>,>>,|,2>, etc)
2. Use grep and regular expression to analyze text

### Shell & Environment Variables
A _shell_ is referred to as the command interpreter, and it is the interface between a user and the Linux kernel. 

A widely used shell by Linux users and administrators is the _bash_ (_bourne-again shell_) shell. Bash is a replacement for the older _Bourne_ shell with numerous enhancements and plenty of new features incorporated from other shells. 

The bash shell is identified by the dollar sign ($) for normal users and the hash sign (#) for the _root_ user. The bash shell is resident in the _/usr/bin/bash_ file.

A _variable_ is a transient storage for data in memory. It retains information that is used for customizing the shell environment and referenced by many programs to function properly. The shell stores a value in a variable, and one or more white space characters must be enclosed within quotation marks ("").

There are two types of variables: _local_ (or _shell_) and _environment_. A local variable is private to the shell in which it is created, and it's value cannot be used by programs that are not started in that shell. This introduces concept of _current_shell and _sub_-shell (or _child_ shell. The current shell is where a program is executed, whereas a sub-shell (or child shell) is created within a shell to run a program. The value of local variable is only available in the current shell.

The value of an environment variable is inherited from the current shell to the sub-shell during the execution of a program. Any environment variable set in a sub-shell is lost when the sub-shell terminates.

There are a multitude of predefined environment variables that are set of each user upon logging in. Use the **env** or the **printenv** command to view their values. Run these commands on localhost as normal user and observe the output. Some of the common predefined environment variables are described in following table.

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

#### Setting and unsetting variables
Shell and environment variables may be set or unset at the command prompt or via programs, and their values may be viewed and used as necessary. We define and undefine variables and view their values using built-in shell commands such as export, unset, and echo. It is recommended to use uppercase letters to name variables so as to circumvent any possible conflicts with a command, program, file, or directory name that exist somewhere on the system. To understand how variables are defined, viewed, made environment, and undefined, a few examples are presented below. We run the commands as normal user.

To define a local variable called VR1:
```shell
[vgnshlvnz@localhost ~]$ VR1=RHEL8
```
To view the value stored in VR1:
```shell
[vgnshlvnz@localhost ~]$ echo $VR1
```
Now type bash at the command prompt to enter a sub-shell and then run echo $VR1 to check whether the variable is visible in the sub-shell. You will not find it there, as it was not an environment variable. Exit out of the sub-shell by typing exit.

To make this variable an environment variable, use the export command:
```shell
[vgnshlvnz@localhost ~]$ export VR1
```

Repeat the previous test by running the bash command and then the echo $VR1. You should be able to see the variable in the sub-shell, as it is now an environment variable. Exit out of the sub-shell with the exit command.

To undefine this variable and erase it from the shell environment:
```shell
[vgnshlvnz@localhost ~]$ unset VR1
```

To define a local variable that contains a value with one or more white spaces:
```shell
[vgnshlvnz@localhost ~]$ VR2="I love RHEL 8"
```

To define and make the variable an environment variable at the same time:
```shell
[vgnshlvnz@localhost ~]$ export VR3 "I love RHEL 8"
```

The echo command is restricted to showing the value of a specific variable and the env and printenv commands display the environment variables only. If you want to view both local and environment variables, use the set command. Try running set and observe the output.

#### Command & Variable Substitutions

The primary command prompt ends in the hash sign (#) for the root user and in the dollar sign ($) for normal users. Customizing this prompt to exhibit useful information such as who you are, the system you are logged on to, and your current location in the directory tree is a good practice. By default, this setting is already in place through the PS1 environment variable, which defines the primary command prompt. You can validate this information by looking at the command prompt 

```shell
vgnshlvnz@localhost ~]$
```

You can also view the value stored in PS1 by issuing echo $PS1. The value is \u@\h \W\$. The \u translates into the logged-in username, \h represents the hostname of the system, \W shows your current working directory, and \$ indicates the end of the command prompt.

Following exercise demonstrates how to use the command and variable subsitution features to customize the primary command prompt for normal user. You will use _hostname_ command and assign its output to PS1 variable. Note that the command whose output you want assigned to a variable must be encapsulated within either backticks `hostname` or parentheses $(hostname). There are two instances of the _variable substitution_ feature employed in the exercise. The LOGNAME and PWD environment variables to display the username and to reflect the current directory location

**Exercise**
In this exercise, you will customize the primary shell prompt to display the information enclosed within quotes "< username on hostname in pwd >" using the variable and command substitution features. You will do this at the command prompt. You will then edit the _~./bash_profile_ file for normal user and define the new value in there for permanence, otherwise, the value will be lost when normal user closes the terminal window or logs off.

1. Change the value of the variable PS1 to reflect the desired information:

```shell
[vgnshlvnz@localhost ~]$ export PS1="< $LOGNAME on $(hostname) in \$PWD >"
<vgnshlvnz on localhost in /home/vgnshlvnz >
```

The prompt has changed to display the desired information.

2.Edit the _.bash_profile_ fir birnak yser abd define the value exactly as it was run in Step 1.

3.Test by logging off as normal user and logging back in. The new command prompt will be displayed.

#### Input, Output, and Error Redirections

Programs read input from the keyboard and write output to the terminal window where they are initiated. Any errors, if encountered, are printed on the terminal window too. This is the default behavior. The bash handles input, output, and errors as character streams. If you do not want input to come from the keyboard or output and error to go to the terminal screen, the shell gives you the flexibility to redirect input, output, and error messages to allow programs and commands to read input from a non-default source, and forward output and errors to one or more non-default destinations.

**EXAM TIP**: You may be asked to run a command and redirect its output and/or error messages to a file.

The default (or the standard) locations for the three streams are referred to as standard input (or stdin), standard output (or stdout), and standard error (or stderr). These locations may also be epitomized using the opening angle bracket symbol (<) for stdin, and the closing angle bracket symbol (>) for stdout and stderr. Alternatively, you may use the file descriptors (the digits 0, 1, and 2) to represent the three locations.

##### Redirecting Standard Input

Input redirection instructs a command to read input from an alternative source, such as a file, instead of the keyboard. The opening angle bracket (<) is used for input redirection. For example, run the following to have the cat command read the /etc/redhat-release file and display its content on the standard output (terminal screen):


```shell
[vgnshlvnz@localhost ~]$ cat < /etc/redhat-release
Red Hat Enterprise Linux release 8.0 (Ootpa)
```


##### Redirecting Standard Output

Output redirection sends the output generated by a command to an alternative destination, such as a file, instead of to the terminal window. The closing angle bracket (>) is used for this purpose. For instance, the following directs the ls command output to a file called Is.out. This will overwrite any existing ls.out file if there is one; otherwise, a new file will be created.

```shell
[vgnshlvnz@localhost ~]$ ls > 1s.out
```

The above can also be run as ```ls 1> ls.out``` where the digit "1" represents the standard output location. 
  
To direct the _ls_ command to append the output to the ls.out file instead of overwriting it, use the two closing angle brackets (>>):
```shell
[vgnshlvnz@localhost ~]$ ls >> ls.put
```

Again, the equivalent for the above is ** ls 1>> ls.out.**
  
  
  
  
  
  
  
  
  
  
