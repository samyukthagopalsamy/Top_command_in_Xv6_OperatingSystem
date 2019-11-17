<b>Using system calls to implement the top command in the XV6 Operating System</b>
XV6 is a simple Unix-like teaching operating system, developed by MIT.<br>
It is an implementation of the Unix version 6 using ANSI C language for x86 platforms.<br>
<b>top</b> is a basic Unix command which provides a dynamic real-time view of the running system. 
The goal of this project is to include the top command in the XV6 environment, similar to that of the one in Linux. This is done by developing a user program which includes system calls to display the list of processes, users of the system resources and the summary information of the system.<br>
The user program included in xv6 displays the Pid, process name, the status of the processes, parent id, and memory size.<br><br>

<b>Follow</b><br>
The xv6 operating system is run on a QEMU virtual machine on top of a 64-bit Ubuntu 18.04.1 LTS machine.<br>
The following tools and packages are to be installed on the Ubuntu Linux system:<br><br>
sudo apt-get update<br>
sudo apt-get install build-essential<br>
sudo apt-get install multi-lib<br>
sudo apt-get install qemu<br>
sudo apt-get install git<br>
<br>
In case of a 64-bit OS, there is a chance that the Makefile will not be able to find qemu. In that case, you should edit the Makefile at line 54 and add the following code:<br>
QEMU = qemu-system-x86_64<br><br>
<b>Install xv6</b> by creating a directory, and clone xv6 to that directory:<br>
$ git clone git://github.com/mit-pdos/xv6-public.git<br><br>
<b>Compile xv6:</b><br>
$make<br><br>
<b>Compile and run the Qemu emulator:</b><br>
$make qemu<br><br>
This will start up a window with QEMU emulator and will start the shell prompt of the xv6 operating system.<br><br>

<b>System call used:</b><br>
<br>
 New system call named <b>sys_top()</b> and <b>sys_date()</b> are included in xv6. To add a system call that can be called in xv6's shell, some changes in the following files have to be made:<br>
•	sysproc.c - the real implementation of the method is added here<br>
•	syscall.h - the position of the system call vector that connects to the implementation is defined here<br>
•	user.h - the function that can be called through the shell is added here<br>
•	usys.S - use the macro to define connect the call of the user to the system call function<br>
<br>
1)	The system call sys_top( ) is included in the file proc.c<br>
2)	<b>#define sys_top 24</b> is included in the file syscall.h<br>
3)	The function void top (void) is added under the system calls in the file user.h<br>
4)	The function syscall(top) is added in the file usys.S<br>
<br>
Since the permission for accessing the p table was denied, the sys_top() system call is included in the file proc.c instead of the file sysproc.c <br>
The included system call sys_top( ) iterates the p-table and gets the pid, process name, status of the processes and the memory size.<br>
By using the system call sys_top( ) the following system information are successfully displayed:<br>
•	Current time, number of users.<br>
•	Total number of tasks running.<br>
•	Memory statistics – total physical memory present, free and used.<br>
•	PID and the corresponding Process name<br>
•	Memory size (KiB)<br>
•	%MEM and Total memory used by the processes<br>
•	State of the processes (running, sleeping, zombie, runnable, unused)<br>

The system information listed above is obtained as follows:<br>
1.	The current system time is obtained using the date system call included in Xv6.<br>
2.	RES is obtained from proc of individual process.<br>
3.	%MEM is obtained by the formula: %mem = (RES / total memory) * 100%. The total physical memory in xv6 is constant which is 0xE000000 (224 MB) <br>
4.	STATE and PID of processes are obtained from proc of current processes which are present in the ptable . ptable can hold a maximum of 64 processes as defined .<br>
![dd](https://user-images.githubusercontent.com/38600655/69007327-612cea80-0962-11ea-9fe4-bae86f17424a.png)
