# Tar Command in Linux

1. Create a basic (uncompressed) archive

```
tar -cvf archive.tar file1.txt file2.txt directory/
```

2. Create a gzip-compressed archive (most common):

```
tar -czvf archive.tar.gz directory/
```

3. Extract files from any compressed archive:

```
tar -xvf archive.tar.gz
```

4. Extract files to a specific directory:

```
tar -xvf archive.tar.gz -C /path/to/destination/
```

5. Extract a single file from an archive:

```
tar -xvf archive.tar.gz path/to/specific_file.txt
```

6. List the contents of an archive without extracting:

```
tar -tvf archive.tar.gz
```

# zip command in linux

1. Compress a single file:

```
zip archive.zip file1.txt
```

2. Compress Multiple Files

```
zip archive.zip file1.txt file2.txt file3.txt
```

3. Compress a directory

```
zip -r archive.zip directory_name/
```

4. Compress all files in the current directory:

```
zip archive.zip *
```

5. View the contents of a zip archive:

```
unzip -l archive.zip
```

6. Extract a zip archive:

```
unzip archive.zip
```

# Environment Variables

### Key Concepts

- Scope: Variables can have two scopes:
  - Shell (local) variables: Exist only in the current shell session and are not passed to child processes.
  - Environment (global) variables: Exported to and inherited by all child processes and subshells. They are typically named in UPPER_CASE by convention.
- Persistence: Variables can be temporary (lasting only for the current session) or permanent (persisting across sessions and reboots).

**Common Commands:**

- List all environment variables: Use printenv or env.
- List all variables (including shell variables): Use the set command.
- Print a specific variable's value: Use echo $VARIABLE_NAME. The $ prefix tells the shell to perform variable expansion.
- Set a temporary environment variable: Use export VARIABLE_NAME="value" in the terminal.
- Remove a variable: Use unset VARIABLE_NAME.

### Setting Persistent Environment Variables

To make variables permanent, you must add them to a configuration file. The appropriate file depends on the desired scope:

| Scope                             | Location                | Command to add (Bash shell)       | Apply changes                      |
| --------------------------------- | ----------------------- | --------------------------------- | ---------------------------------- |
| User-specific (current user only) | ~/.bashrc or ~/.profile | export VARIABLE_NAME="value"      | Run source ~/.bashrc or log out/in |
| System-wide (all users)           | /etc/environment        | VARIABLE_NAME="value" (no export) | Log out and log back in, or reboot |

### Common Environment Variables

| Variable  | Description                                                                       |
| --------- | --------------------------------------------------------------------------------- |
| PATH      | A colon-separated list of directories the shell searches for executable commands. |
| HOME      | The current user's home directory.                                                |
| USER      | The name of the currently logged-in user.                                         |
| SHELL     | The path to the current user's default shell interpreter (e.g., /bin/bash).       |
| PWD       | The present working directory.                                                    |
| EDIT      | OR The system's default text editor (e.g., nano, vim).                            |
| $HOSTNAME | The network name of the machine.                                                  |

# Working with ReGex

A regular expression is a sequence of characters that forms a search pattern. It can include literal characters, metacharacters, and quantifiers to define a specific pattern.

- Literal Characters: Matches the exact character (e.g., apple matches "apple").
- Metacharacters: Special characters that define rules (e.g., ^, $, .).

1. Metacharacters

Metacharacters have special meanings in regular expressions:

| Symbol | Name            | Function                                             | Example | Matches                     |
| ------ | --------------- | ---------------------------------------------------- | ------- | --------------------------- |
| .      | Dot             | Matches any single character except newline          | b.t     | bat, bet, bit, b@t          |
| ^      | Caret           | Anchors to the start of the line.                    | ^Error  | Lines starting with "Error" |
| $      | Dollar          | Anchors to the end of the line.                      | done$   | Lines ending with "done"    |
| []     | Character Class | Matches one character from a specified set or range. | [Rr]ead | Read, read                  |
| [^...] | Negation        | Matches one character NOT in the specified set.      | [^0-9]  | Any non-digit character     |

2. Quantifiers
   Quantifiers specify the number of occurrences of a character or group:

- \*: Matches 0 or more occurrences.
- +: Matches 1 or more occurrences.
- ?: Matches 0 or 1 occurrence.
- {n}: Matches exactly n occurrences.
- {n,}: Matches n or more occurrences.
- {n,m}: Matches between n and m occurrences.

Eamples:

1. Using grep

```
grep "pattern" filename

grep "apple" regextut.txt
```

2. Using sed

```
sed 's/pattern/replacement/g' filename
```
