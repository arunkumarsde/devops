# pwd, cd, ls, and clear commands

## pwd (print working directory)

```
pwd -> /home/john/Documents/Projects
pwd -L -> Prints the symbolic path.
pwd -P -> Prints the actual path.
$PWD -> Global variabale that stores the path of the current working directory.
/bin/pwd -> displays the absolute path of the current working directory
```

## ls [options] [path] -> lists directory contents

```
ls -l -> Long Listing. Shows details like file permissions, owner, size, and modification date.
ls -a -> All. Shows hidden files (files starting with a dot ., like .bashrc).
ls -A -> Exludes (. and .. folders)
ls -lh -> Long + Human Readable. Shows file sizes in KB, MB, GB instead of bytes.
ls -R -> Recursive. Lists all files in the current folder AND all sub-folders.
ls \*.pdf -> list files matching pattern
ls -t -> lists all the files in the folder which is sorted by time (newest first).
ls -t | head -1 -> prints newly created file
ls -1 -> One file in one line
```

**Example:**

```
-rw-rw-r-- -> means read-write for user, read-write for group, and read-only for others.
-rwxrwxrwx -> means full access for all.
```

- **ls -lh**:
  -l: lists files in long format (with permissions, owner, size, and date)
  -h: shows the file size in a human-readable format like KB, MB, or GB instead of just raw bytes

- **ls -l /dirctory**: List content of the directory
- **ls -ld /directory**: see the details about a directory itself
- **ls -lt**: It lists all files and folders sorted by the last modified time

- **ls -ltr**:
  -l: shows detailed info like size, permissions, and modification time
  -t: sorts files by last modified time (newest first)
  -r: reverses the order (oldest first)

- **ls -i [path]**: To see the inode number of a file or folder.

```
Note: Every file in Linux has a unique ID number called an inode number. This number keeps track of important details about the file like its location on disk, size, permissions, and timestamps but not its name.
```

- **ls -q** - Sometimes, files or folders may have irregular or non-printable characters which is called control characters in their names basically they can mess up your terminal display or be confusing to read.

Use ls -q command is a safe way to list files while hiding those confusing characters.

- **ls -n [path]** - It shows the UID (User ID) and GID (Group ID) of the files and folders instead of just showing usernames and group names.

This command tells you which user and group actually owns a file or folder by showing the UID (User ID) and GID (Group ID) of the files and folders instead of just showing usernames and group names.

- **ls -F**: The ls -F command in Linux helps you quickly identify file types by adding special symbols at the end of each file name.

/ – directory.
nothing – normal file.
@ – link file.

- – Executable file

* **ls --color=auto**: Colors the files based on types.
* **ls -l --time-style=long-iso**: This command will display the time in YYYY-MM-DD HH:MM format.
  There are other options like

```
locale
full-iso
iso
```

## cd [path] (change directory)

```
   cd documents -> Moves into documents
   cd .. -> Moves to the parent directory
   cd ~ -> Moves to home directory
   cd -** Moves back of previous directory (similar to back button)
```

## clear -> Clears screen

```
clear
clear -x
reset
printf "\ec" -> clear the terminal
```

The `clear` ommand clears the terminal screen only while when talk about `reset` command, it not only clear the screen but also resets the terminal's settings to their default values.

## echo command

**Syntax:**

```
echo [option] [string]
```

```
Note: -e enables the interpretation of backslash escapes.
```

1. echo -e "Linux \bnotes": -b removes spaces.

**Output:**

```
Linuxnotes
```

2. echo -e "An apple \cpie": \c removes trailing characters.

**Output:**

```
An apple
```

3. echo e "An \napple \n pie: -n Adds a new line character
   **Output:**

```
An
apple
pie
```

More commands:

```
\t : Adds a tab
\r : Returns succeeding character (Opposite of \c)
\v : Adds vertical tabs
\a : Alert return
echo * : prints all files/folder similar to ls
echo "Notes" > output.txt
```

# sudo, id, and su commands

## id -> uid=1000(john) gid=1000(john) groups=1000(john),4(adm),27(sudo),113(docker)

- uid (User ID): Your unique numeric identifier. 1000 is typically the first standard user. 0 is always root.
- gid (Group ID): Your primary group.
- groups: A list of all groups you belong to.
- Why this matters: If you don't see docker here, you can't run Docker commands. If you don't see sudo or wheel, you likely can't use sudo.

**Syntax:**

```
id [OPTION]… [USER]
```

**Options:**

- '-g': Prints only the effective group ID of the user.
- '-G': Prints all group IDs associated with the user.
- '-n': Displays names instead of numeric IDs.
- '-r': Shows the real ID instead of the effective one.
- '-u': Prints only the effective user ID.
- '--help': Displays help messages for the id command and exits.
- '-version': Shows the version information and exits.

**Example:**

```
id -g master
id -G master
```

## sudo (SuperUser Do)

sudo allows a permitted user to execute a single command as the superuser (root) or another user, as specified by the security policy (typically /etc/sudoers).

Why use it? It is safer than logging in as root because:

- Granularity: You grant specific permissions.
- Audit Trail: Every sudo command is logged (/var/log/auth.log), so you know who did what.
- Safety: It asks for YOUR password (to prove you are you), not the root password.

```
Syntax: sudo [command]
```

**Common Examples:**

- Install Software: sudo apt install python3
- Edit System Config: sudo nano /etc/hosts
- Restart Services: sudo systemctl restart nginx
- Run as specific user: sudo -u postgres psql (Runs psql as user postgres)

## su (Switch User)

The su command lets you switch your current session to another user account entirely. Unlike sudo (which asks for your password), su asks for the Target User's Password.

```
Syntax: su [options] [username]
```

The Critical Difference: su vs su -

This is the most common mistake beginners make.

a. su username (Without dash): - Switches user identity. - Retains your old environment variables (current directory, PATH, custom aliases). - Risk: Can cause errors if the new user's tools expect a clean environment.
b. su - username (With dash - Recommended): - Switches user identity. - Resets the environment variables to the target user's defaults. - Simulates a "Fresh Login." This is almost always what you want.

Eg.

```
# Switch to root (Requires root password)
$ su -

# Switch to user 'bob' (Requires bob's password)
$ su - bob
```

**Summary: When to use which?**

_sudo_

- Running one or two administrative commands.
- Requires Your password.
- High. Logs commands and limits scope.
- Preserves most env variables (configurable).

_su -_

- starting a long session as another user.
- Requires Target user's password (e.g., Root).
- Lower. Sharing root password is bad practice.
- Resets environment (Clean slate).

```
sudo ls /root -> list files in root folder
sudo nano /etc/hosts -> modify files as root user
sudo -u nobody whoami -> -u nobody: tells sudo to run the command as the user nobody, and whoami displays the name of the current effective user
sudo -l -> list all allowed commands
sudo -v -> validate credentials
sudo -k -> forces to ask for password
sudo -s -> run a shell as root user
sudo -H nano /root/.bashrc -> Set Home Environment variable
sudo -b sleep 60 -> run command in background
sudo -p "custom_message" ls /root -> display a custom message when sudo asks for a password
sudo -- -version -> The -- option tells sudo to stop processing options.
```

**Example:**

Start a 60-second sleep process in the background and confirm that it is running using ps:

```
sudo -b sleep 60
ps aux |  grep sleeep

sudo -p "Enter your password to continue: " ls /root

sudo -- -mycmd -> Run a command named -mycmd withoudo treating it as an option.
```

## cp and mv commands

```
cp [source] [destintion]

cp file.txt backup.txt -> overwrites if the destination file exists
cp docs/file1.txt docs/file2.txt docs2/**
cp -r or cp -R -> copy all content of a directory
cp -i -> To force a confirmation prompt before overriding file
cp -p -> Use -p to copy the file while keeping the original Creation Date, Permissions, and Ownership.
cp -u -> Only copies the file if the source is newer than the destination, or if the destination is missing. Great for syncing folders.
```

```
mv [source] [destination]


mv file1.txt file2.txt -> Renames the file if doesn't exist or overwrite the file
mv -n (no clobber) -> Do not overwrite, If the destination file already exists, mv will simply skip it and do nothing
mv -v photo.jpg /tmp/ -> output: 'photo.jpg' -> '/tmp/photo.jpg', Shows you exactly what is happening.
```

### rename and touch commands

```
touch newfile.txt - Creates a new file
touch existingfile.txt - Changes access and modified time
touch -c config.json - If the file doesn't exist, do nothing.
touch -a log.txt - Updates access time only
touch -m log.txt - updates modified time only
touch -t 202501011200 file.txt (Sets date to Jan 1, 2025, 12:00) - Set a Specific Time (Format: [[CC]YY]MMDDhhmm[.ss]).
```

# mkfir, rmdir, and rm commands

## mkdir [directory_name]

```
mkdir photos -> creates a new directory
mkdir -p projects/web/src -> The -p flag tells Linux: "Create any missing parent folders automatically."
```

## rm_dir [directory_name]

```
rmdir photos -> removes an empty directory
rmdir -p /path/to/empty/directory -> Removes parent directories if they become empty after removing the specified directory.
rmdir -p /path/to/empty/nested/directories -> Remove Nested Empty Directories
```

## rm [file_path]

```
rm data.txt -> removes data.txt
rm -r my_folder -> removes all files in the directory
rm -i -> Prompts you for confirmation before deleting every single file.
rm -f -> Ignores non-existent files and never prompts for confirmation. It suppresses most error messages.
rm -v -> Lists every file being deleted as it happens.
rm -rf /path/to/folder -> forcefully removes all files
```

# head and tail Command

```
head sample.txt -> prints the first 10 lines
head -n 2 sample.txt -> print only 2 lines

tail sample.txt -> print the last 10 lines
tail -n 2 sample.txt -> print the last 2 lines
```

# cat and tac command

```
cat file1.txt file2.txt -> prints the contents of the files in the order they appear

tac file1.txt file2.txt -> prints the contents in reverse order
```

**More `cat` Examples:**

```
cat > file_name -> create a new file
cat file1 > new_file -> copy content to new_file
cat file1 file2 > new_file -> use to merge two files
cat file_name1 >> file_name2 -> Appends the content of file1 to file2
cat -n file_name -> also prints line number
cat -s file_name -> Removes multiple empty lines from the output
cat -E file_name -> print and add $ at the end of each line
cat -A file_name -> used to make non-printing characters visible in the output
cat -- -jayesh2 -> open a files starts with `-` (When a filename begins with a dash (-), Linux may treat it as a command option.)
cat *.txt -> Prints contents of all file matching the pattern
```

```
cat file.txt -> output
cat >> file.txt
This is new line
```

The above content is appended to the file.txt

**More `tac` example:**

```
tac -b concat.txt tacexample.txt -> attaches the separator before the line.
tac -r concat.txt tacexample.txt -> Interpret the Separator as a Regular Expression
tac -s concat.txt tacexample.txt -> The -s option allows you to specify a custom string as the separator.
```

## more and less Commands

### more

The command used to display the contents of a file one page at a time.

```
more sample.txt
```

**Syntax**

```
more [-options] [-num] [+/pattern] [+linenum] [file_name]
```

where,

- [-options]: any option that you want to use in order to change the way the file is displayed. Choose any one from the followings: ('-d', '-l', '-f', '-p', '-c', '-s', '-u')

- [-num]: type the number of lines that you want to display per screen.
- [+/pattern]: replace the pattern with any string that you want to find in the text file.
- [+linenum]: use the line number from where you want to start displaying the text content.
- [file_name]: name of the file containing the text that you want to display on the screen.

### Navigating Through Text Files Using the 'more' Command

While viewing text files with 'more', you can use the following controls for navigation:

- Enter key: Scrolls down one line at a time.
- Space bar: Moves to the next page or screen.
- 'b' key: Goes back one page.
- 'q' key: Quits the more command and exits the view.
- '=' key: Show current line number.

### less

The less command in Linux is used to view the contents of a file one page at a time without opening it in an editor, making it ideal for reading large files efficiently.

**Syntax**

```
less [options] filename
```

- -E: Automatically exits when the end of the file is reached.
- -f: Forces opening of non-regular files (such as special files).
- -F: Exits immediately if the entire file fits on the first screen.
- -g: Highlights only the last searched string.
- -G: Disables highlighting of all search matches.
- -i: Ignores case while searching (case-insensitive search).
- -n: Hides line numbers in the output.
- -p pattern: Opens the file at the first occurrence of the specified pattern.
- -s: Combines multiple consecutive blank lines into a single blank line.

## cut command

The cut command in Linux is used to extract specific parts of each line from a file or input. You can extract data based on byte position, character position, or fields separated by a delimiter. It is mainly used to select and display required columns from text files.

```
cat sample.txt
```

_Examples:_

- cut -b 1,2,3 state.txt -> extracts first three bytes
- cut -b 1-3,5-7 state.txt -> extract characters from positions 1 to 3, and then from 5 to 7
- cut -b 1- state.txt -> 1- means from byte 1 to the last byte
- cut -b -3 state.txt -> -3 means extract from byte 1 to byte 3

## grep command

The grep command is one of the most useful tools in Linux and Unix systems. It is used to search for specific words, phrases, or patterns inside text files, and shows the matching lines on your screen.

```
grep [options] pattern [files]
```

**Examples:**

- grep -i "UNix" geekfile.txt -> -i option enables to search for a string case insensitively
- grep -c "unix" geekfile.txt -> find the number of lines that matches the given string/pattern
- grep -l "unix" \* -> display the files that contains the given string/pattern.
- grep -l "unix" f1.txt f2.txt f3.xt f4.txt -> display the files that contains the given string/pattern.
- grep -o "unix" geekfile.txt -> display only the matched string
- grep -n "unix" geekfile.txt -> show the line number of file with the line matched.
- grep -v "unix" geekfile.txt -> display the lines that are not matched with the specified search string pattern

## comm, sort, tee and sed commands

### comm command

The 'comm' command in Linux is a powerful utility that allows you to compare two sorted files line by line, identifying the lines that are unique to each file and those that are common to both. This command is particularly useful when you have lists, logs, or data sets that need to be compared efficiently.

```
$comm [OPTION]... FILE1 FILE2
```

### sort command

The sort command in Linux is used to sort a file, arranging the records in a particular order. By default, the sort command sorts file assuming the contents are ASCII. Using options in the sort command can also be used to sort numerically.

```
sort file.txt
```

### tee command

The tee command reads standard input and writes it to both standard output and one or more files at the same time. It’s usually used with pipes and filters to duplicate the input into each output. The name comes from the T-shaped pipe fitting in plumbing, as it splits the data flow just like a pipe tee splits water.

```
tee [OPTION]... [FILE]...

cat file.txt | tee new_file.txt
```

### sed command

The sed command in Linux, short for stream editor, is a non-interactive text editor used to perform basic text transformations on an input stream, such as a file or input from a pipeline. It processes text line by line, applying the editing commands you specify.

Internally, sed works with two workspaces: the pattern space, which holds the line being modified, and the hold space, which can temporarily store a line for later use.

```
sed 's/unix/linux/' geekfile.txt -> replace unix with linux in output (not in file)
sed 's/unix/linux/2' geekfile.txt -> replace second occurance
sed 's/old_word/new_word/g' filename -> replace all occurances
sed 's/unix/linux/3g' geekfile.txt -> from nth occurance replace all
sed 's/unix/linux/ig' geekfile.txt -> case sensitive
sed -i 's/unix/linux/3g' geekfile.txt -> replace in file
```

## tr, uniq and wc commands

### tr command

The tr (translate) command in Linux is used to transform or delete specific characters from text input. It is commonly used for tasks like changing letter cases, removing unwanted characters, or compressing repeating characters.

```
cat test.txt | tr [a-z] [A-Z]
cat test.txt | tr [:lower:] [:upper:]
tr [:lower:] [:upper:] < test.txt
```

### uniq command

The uniq command in Linux is used to detect, report, or remove adjacent duplicate lines from a text file or standard input. It helps in cleaning and organizing data by displaying only unique entries or counting repetitions.

uniq kt.txt -> remove duplicate lines from `kt.txt`

### wc command

The wc command in Linux is used to count lines, words, characters, and bytes in a file or from input you provide. Whether you're analyzing logs, reading large datasets, or just checking the size of your scripts, wc gives a quick summary. It can take one or more files as input or read directly from user input if no file is mentioned.

```
wc state.txt
```
