# Copying File (cp command)
The cp command has 3 modes depending on the types and number of arguments passed to it

## Mode 1
If the command has 2 arguments of type **file**, it copies the contents of the first file into the second

```
cp file1 file2
```

If the second file does not exist, **cp** will create it, if it exists, **cp** will override the contents with that of ***file1***

To prompt the user before overriding an existing file pass the *-i* options

```
cp -i file1 file2
```

## Mode 2
**cp** has one of more arguments of type **file** which are the source files and the last argument is of type **directory** which is the destination.

In this case, **cp** copies the sources files to the destination directory, creating any files that don't already exist in the destination and overriding existing ones
```
cp file1 file2 ... fileN directory
```

## Mode 3
When all the arguments to **cp** are of type directory, in this case, **cp** copies all the files in source directories to the destination directory (the last directory), overriding any file that already exists. This require the ***-r*** (recursive) flag.
```
cp -r directory1 directory2 directory3
```

Copies all the contents of ***directory1*** and ***directory2*** to ***directory3** under the nested folders ***directory3/directory1*** and ***directory3/directory2***.

### Note
The logged in user who copies a file becomes the owner of the copied file (the original owner of the source file remains the owner of those files, but not the copies). If we want to keep the owner of the source files as the owner of the copies, we use th ***-p*** (preserve) option to preserve file attributes.