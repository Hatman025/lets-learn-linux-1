Devsecops Parocyber Assignment 1
Question 1
Task: Print your current directory, your username, and full OS and kernel details — three separate commands.
1)	Current directory: (pwd), display the current directory a user is
2)	Username: (whoami), this displays the current user logged on
3)	Uname -r: display the full OS and the kernel details of the linux distribution
A DevSecOps engineer run these three things first when SSHing into an unknown server to determine the who they are operating as, the current directory they landed on, they need to know what the system is running on a foundational level.

 

Question 2
Task: List the contents of the root directory — the very top of the Linux filesystem.
In Linux, the root directory (/) is the absolute base of the entire filesystem. Every single file, folder, and mounted drive branches out from this point. It includes following directories;
1)	bin: it contains single-user binary executable commands that are required to boot the system, which contains command like ls, cd, pwd    
2)	dev: this means device files and the /dev directory is a virtual directory that represents the system's hardware devices as files. Applications interact with these files to talk to physical hardware.
3)	home: This is the personal storage space for regular users. It keeps user data completely separated from system data.
4)	tmp: this directory is used to store temporary files needed during active sessions. Such as Cache files, session locks, or temporary installation files.
5)	usr: it short for "Unix System Resources". It holds the vast majority of user utilities, libraries, and applications that are not critical for basic system booting. Including /usr/share
The Linux directory structure follows standard called the Filesystem Hierarchy Standard (FHS). This ensures files are in predictable locations in most Linux distributions

 
Question 3
Task: List your home directory twice — once the default way, then again revealing hidden files with full details (permissions, ownership, size).
 
1) The flag “ls -la” display hidden files, the period (.) at the beginning of a file makes it hidden.
2) The (.bash_logout) file is a hidden shell script that runs automatically every time a user exit an interactive login shell.
3) The .face file is a hidden image file used by some Linux operating systems to display your user account profile picture.
   

SECTION 2
Question 4
 Create the entire structure above in a single command — including all nested subdirectories. Then display the full tree to verify.
Brace expansion is a Bash feature that generates multiple strings from a pattern containing curly braces {}. It's a quick way to produce lists of strings without repetition.

 
Question 5
Task: Create these five empty files in a single command: configs/app.conf, configs/db.conf, logs/access/access.log, logs/errors/error.log, reports/weekly_report.txt
The touch command is used to create an empty file and also change the time stamp of an existing file. Timestamps are a lightweight signaling mechanism, changing them can start or skip processes without touching the actual file content. That's why accidentally updating a timestamp in the wrong place can cause unexpected rebuilds, skipped syncs, or broken caches.
 

Question 6
Task:  ~/projects /configs/ in long format with human-readable file sizes.
1) -l — long format (which shows the permissions, owner, size, timestamp) 
2) -h — human-readable sizes (KB, MB, GB instead of raw bytes)
3) The file size is 0 because the file is empty.
4) This new file has a permission of 664, where the owner/user has the read & write permission, the group has the read & write permission and the other has only the read permission.
 
Question 7
Task: Display the full tree of ~/projects/cyphercore again
The “tree” command displays directory in hierarchical tree-like structure while the “ls -R” display directories recursively, showing content of each directory after the other
 
SECTION 3
Question 8
Task: Write each of the three log lines into logs/access/access.log using echo, appending each one. Read the file back to confirm all three lines exist.
1) The “>” operator is use to overwrite the content of a file while “>>” is use to append / add data to the content of a file.
2) Confusing these two operators is dangerous in a production log rotation script because using the > instead of >> overwrite the script, thereby deleting the needed log script. once a file is overwritten with >, you usually cannot restore it directly unless you had a backup or use specialized recovery tools.
  
Question 9
Task: Display the access log using two different reading commands — one from top to bottom, one from bottom to top.
1) The “cat” function displays the content of a file the exact way from the top to bottom while the “tac” function display the content of a file from the bottom to the top. 
2) In a real incident, where a log has 80,000 lines and the most recent events are at the bottom, the “tac” function should be used.
3) The “tail” function shows the last ten lines of a file.
 
 
Question 10
Task: Use heredoc notation to append all four lines into logs/errors/error.log in one operation. Read it back 
1) A heredoc (short for here document) is a way to feed a block of text directly into a command in Linux, without needing multiple echo statements. It’s especially useful when you want to write or append several lines into a file in one go.
2) An unquoted EOF is used when you want dynamic values inside the heredoc, and quoted 'EOF' is used when someone wants the text exactly as written (like when writing scripts or config files that should contain $DBNAME literally).
3) Heredoc is perferred because it lets you inject multiple lines into a file in one command, herecdoc can be scripted making it useful for quick changes without manual editing.Editors like nano or vim requires keystrokes which can leave mores traces in shell history and terminal logs, heredoc appears only in the shell history and easier to blend into normal scripting activity.
  

Question 11
Task: Open the error log using two different pager commands. Navigate and quit each one.
1)	The difference is that “more” is a simpler, older pager that only allows limited forward navigation, while “less” is a more advanced pager that supports both forward and backward movement, searching, and richer navigation features. In practice, less is far more flexible and is the default pager in most modern Linux systems. 
2)	Neither less nor more loads the entire file into memory first.”more” streams forward only, minimal memory use.”less” streams but keeps a buffer, allowing backward navigation and advanced features.On a server with 512MB RAM and a 3GB log, used the “less” pager is preferable.
3)	“/pattern and ?pattern” is use to search forward and backward with less, “G” is use to jump to he end of the page and “q” is use to quit the pager.
 

Question 12
Task:
•	Copy the error log to the archive folder as error_2025-06-02.log
•	Rename the access log to access_2025-06-02.log
1) The fundamental difference between the “cp” & the “mv” functions is that the ‘copy’ command creates a duplicate of the file of folder while the original file remain in place and a new file is created at the destination the ‘move’ is use to rename and completely move a file from one destination to another.
2) After renaming with the ‘cp’ command the original filename exist in the original directory
3) when you run cp on a large file, the system doesn’t instantly duplicate all the data on disk in one shot. Instead, it streams the file contents from the source to the destination

 
Question 13
Task:
•	Create an empty directory reports/temp_drafts
•	Remove it using the command designed for empty directories
•	Attempt to remove the entire logs/ directory using that same command
1) When I attempt to delete the “logs” directory using the rmdir function, it was not successful because the directory is not empty
2) The rmdir delete empty directory while the rm -r is dangerously because delete a directory with all it contents recursively.
3) Together, this means rm -rf will silently and recursively delete everything you point it at, even entire directory trees without any warnings or confirmation. If misused (e.g., rm -rf /), it can wipe out the entire operating system.
4) An engineer should list the content of a directory and be absolute sure before using the “rm -rf” commands, because it can delete an entire operating system if misused.

 
Question 14
Task: Display the full tree of ~/projects/cyphercore.
1) Yes, the matches 
2) A clean, predictable directory structure is needed for reliability, scalability, consistency, error-reduction and security in automated systems.
 
Question 15
Task:
•	Write three lines into configs/db.conf: DB_HOST=10.0.0.10, DB_PORT=5432, DB_NAME=cyphercore_prod
•	Create a hard link to it in the same directory named db_hardlink.conf
•	List the configs directory showing inode numbers alongside full file details
1) The number of the orginial file and the hardlink file is the same “3935645”.
2) The link count shows how many directory entries (names) point to the same inode.
3) Inode stores information about a file but not the filename.
 
Question 16
Task: Delete the original db.conf. Then read the hard link file.
1) When the original file was deleted the data same remains in the hardlink file
2) The link count changes to 1 when the original file was deleted
3) For the data to be deleted permanently the hardlink file will have to be  deleted as well
4) An attacker could create a hard link in an obscure directory, then delete the obvious file , the data still exists but is hidden under a different name.

 
Question 17
Task:
•	Write some content into configs/app.conf
•	Create a symbolic link to it inside ~/projects/cyphercore/ named app_config_link
•	List ~/projects/cyphercore/ showing full details including link targets
•	Delete the original app.conf and try to read the symlink
1) The (->) arrow symbol identifies a file been softlink to another filename
2) An error that ‘no such file or directory exist’ is displays when I tried reading a broken link. This type of situation is called a dangling symlink.
3) Dangling symlink in a deployment pipeline cause failures that are hard to debug at 2 AM because depending on the stage, environment, or caching, the broken link may only fail under certain condition, making it hard to reproduce.The symlink may work in dev but fail in prod if the target file wasn’t deployed and on call engineers are tired and under pressure to restore service quickly.
 
 
Question 18
Property	Hard Link	Soft Link
Shares inode with original?	yes	no
Works across different filesystems?	no	yes
Survives deletion of original?	yes	no
Can link to a directory?	no	yes
Shows as l in ls -la?	no	yes
Detectable by matching inodes in ls -li?	yes	no

1) Hardlinks are used for Immutable audit logs: Security teams sometimes create hard links to critical log files (e.g., /var/log/auth.log) in a protected directory.
2) Softlinks are used for Versioned configuration management: In deployment pipelines, symlinks are often used to point to the current version of a config or binary.
Question 19
Task:
•	Run a single ls targeting two paths: one that exists (logs/) and one that does not
•	Redirect normal output to reports/stdout_output.txt
•	Redirect error output to reports/stderr_output.txt
•	Read both files back

•	stdin   (0)  ←  input
•	stdout  (1)  →  normal output
•	stderr  (2)  →  error output
•	The “2>” explicitly redirects stderr (file descriptor 2) to reports/stderr_output.txt
 
Question 20
Task: Write the following report into reports/weekly_report.txt using a heredoc
1)	The delimiter was not quoted because I want it to extend into the variable
2)	The delimiter with quote does not extend to the variable while the delimiter without quote extends to the variable
3)	An unquoted delimiter is used when we want the heredoc to behave like normal shell input, expanding variables and commands while a quoted heredoc is used when we are writing documentation, code snippets, or config files where $ and backticks should remain untouched.
 
 

Question 21
1)	The below screenshots demonstrate how directories and files can be managed, manipulated by both analyst and an intruder. A devsecops engineer need to have a good understanding of the commands and structure of how to properly investigate and manage these files throughout the lifecycle of software development.
2)	The hardlink and softlink (ln and ln -s) has made me understand that malicious files can be hidden in plain sight and not to assume some system filename can’t be malicious.
3)	The mkdir with the -p flag has made me understand that multiple directories can be created using one command, which made me see more advantage of CLI to GUI.
 





