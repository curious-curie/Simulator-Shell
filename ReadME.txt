
CS 324, Winter 2018
Lab Assignment: Writing Your Own Unix Shell
Due: Friday, Feb. 2, 11:59PM
Introduction
The purpose of this assignment is to become more familiar wit
h the concepts of process control and sig-
nalling. You’ll do this by writing a simple Unix shell progra
m that supports job control.
Logistics
You will work on your own solving the problems for this assign
ment. The only “hand-in” will be electronic.
Any clarifications and revisions to the assignment will be po
sted on the course Web page.
Hand Out Instructions
Start by downloading the file
shlab-handout.tar
from the LearningSuite assignment page corre-
sponding to this assignment. Save it to the protected direct
ory (the
lab directory
) in which you plan to
do your work.
•
Type the command
tar xvf shlab-handout.tar
to expand the tarfile.
•
Type the command
make
to compile and link some test routines.
•
Type your name and BYU netid in the header comment at the top of
tsh.c
.
Looking at the
tsh.c
(
tiny shell
) file, you will see that it contains a functional skeleton of a
simple Unix
shell. To help you get started, we have already implemented t
he less interesting functions. Your assignment
is to complete the remaining empty functions listed below. A
s a sanity check for you, we’ve listed the
approximate number of lines of code for each of these functio
ns in our reference solution (which includes
lots of comments).
•
eval
: Main routine that parses and interprets the command line. [
70 lines]
1
•
builtin
cmd
: Recognizes and interprets the built-in commands:
quit
,
fg
,
bg
, and
jobs
. [25
lines]
•
do
bgfg
: Implements the
bg
and
fg
built-in commands. [50 lines]
•
waitfg
: Waits for a foreground job to complete. [20 lines]
•
sigchld
handler
: Catches SIGCHILD signals. 80 lines]
•
sigint
handler
: Catches SIGINT (
ctrl-c
) signals. [15 lines]
•
sigtstp
handler
: Catches SIGTSTP (
ctrl-z
) signals. [15 lines]
Each time you modify your
tsh.c
file, type
make
to recompile it. To run your shell, type
tsh
to the
command line:
unix> ./tsh
tsh>
[type commands to your shell here]
General Overview of Unix Shells
A
shell
is an interactive command-line interpreter that runs progr
ams on behalf of the user. A shell repeat-
edly prints a prompt, waits for a
command line
on
stdin
, and then carries out some action, as directed by
the contents of the command line.
The command line is a sequence of ASCII text words delimited b
y whitespace.  The first word in the
command line is either the name of a built-in command or the pa
thname of an executable file. The remaining
words are command-line arguments. If the first word is a built
-in command, the shell immediately executes
the command in the current process. Otherwise, the word is as
sumed to be the pathname of an executable
program. In this case, the shell forks a child process, then l
oads and runs the program in the context of the
child. The child processes created as a result of interpreti
ng a single command line are known collectively
as a
job
. In general, a job can consist of multiple child processes co
nnected by Unix pipes.
If the command line ends with an ampersand ”
&
”, then the job runs in the
background
, which means that
the shell does not wait for the job to terminate before printi
ng the prompt and awaiting the next command
line. Otherwise, the job runs in the
foreground
, which means that the shell waits for the job to terminate
before awaiting the next command line. Thus, at any point in t
ime, at most one job can be running in the
foreground. However, an arbitrary number of jobs can run in t
he background.
For example, typing the command line
tsh>
jobs
causes the shell to execute the built-in
jobs
command. Typing the command line
tsh>
/bin/ls -l -d
runs the
ls
program in the foreground. By convention, the shell ensures
that when the program begins
executing its main routine
2
int main(int argc, char
*
argv[])
the
argc
and
argv
arguments have the following values:
•
argc == 3
,
•
argv[0] == ‘‘/bin/ls’’
,
•
argv[1]== ‘‘-l’’
,
•
argv[2]== ‘‘-d’’
.
Alternatively, typing the command line
tsh>
/bin/ls -l -d &
runs the
ls
program in the background.
Unix shells support the notion of
job control
, which allows users to move jobs back and forth between back-
ground and foreground, and to change the process state (runn
ing, stopped, or terminated) of the processes
in a job. Typing
ctrl-c
causes a SIGINT signal to be delivered to each process in the f
oreground job. The
default action for SIGINT is to terminate the process. Simil
arly, typing
ctrl-z
causes a SIGTSTP signal
to be delivered to each process in the foreground job. The def
ault action for SIGTSTP is to place a process
in the stopped state, where it remains until it is awakened by
the receipt of a SIGCONT signal. Unix shells
also provide various built-in commands that support job con
trol. For example:
•
jobs
: List the running and stopped background jobs.
•
bg <job>
: Change a stopped background job to a running background job
.
•
fg <job>
: Change a stopped or running background job to a running in th
e foreground.
•
kill <job>
: Terminate a job.
The
tsh
Specification
Your
tsh
shell should have the following features:
•
The prompt should be the string “
tsh>
”.
•
The command line typed by the user should consist of a
name
and zero or more arguments, all sepa-
rated by one or more spaces. If
name
is a built-in command, then
tsh
should handle it immediately
and wait for the next command line. Otherwise,
tsh
should assume that
name
is the path of an
executable file, which it loads and runs in the context of an in
itial child process (In this context, the
term
job
refers to this initial child process).
•
tsh
need not support pipes (
|
) or I/O redirection (
<
and
>
).
3

