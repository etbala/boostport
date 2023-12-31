# Getting Started on z/OS

## Setting up SSH

To connect to the z/OS system, we require your public SSH key. If you are unsure how to generate and retrieve your public SSH key, please follow the steps outlined below:

### Windows keygen instructions

Open your terminal or command prompt.

Type the command `ssh-keygen -t rsa` and press Enter. This will generate a new SSH key pair at `/c/Users/<username/.ssh` by default.

If you chose the default location, your public key will be available at `/c/Users/<username>/.ssh/id_rsa.pub`. Provide the contents of this file to the admin.

### Linux/macOS keygen instructions

Open your terminal or command prompt.

Type the command `ssh-keygen -t rsa` and press Enter. This will generate a new SSH key pair at `~/.ssh` by default.

If you chose the default location, your public key will be available at `~/.ssh/id_rsa.pub`. Provide the contents of this file to the admin.

### Setting up a config file

To connect to z/OS without typing out your username and the IP, you can set up a config file. To do this you can create a config file in your .ssh directory (`~/.ssh/config` on linux/macOS). Once you have created the file, add the following to the file.

````
Host <Alias of your choosing>
   HostName 128.xxx.xxx.xxx
   User <Admin provided username here>
   IdentityFile ~/.ssh/id_rsa
````

### Connecting to z/OS

Once the admin has added your public key, you should be able to connect to z/OS using `ssh username@128.xxx.xxx.xxx`. 

If you set up a config file, you can connect using `ssh <alias you assigned>`.

If you get a permission error, it is possible you need to alter the permissions of your private key so only you can view/edit it. You can do this by running `chmod 600 ~/.ssh/id_rsa`.

If ssh cannot find your private key, you can specify the file path to the private key in the command using: `ssh username@128.xxx.xxx.xxx -i /path/to/private/key/id_rsa`.

## Getting Started on z/OS 

### .profile

For setting up the .profile, there are a few things that are recommended, but not required. The following commands are beneficial to add.

```
. /UserName/zopen/etc/zopen-config
bash
export PATH="/usr/lpp/IBM/oelcpp/v2r0/bin:$PATH"
```

This will source zopen-config, the second line will put you directly into bash, and the third line will add the CLANG compiler to your current path.


# Contributing Guidelines

## Issues

Log an issue for any question or problem you might have. When in doubt, log an issue, and
any additional policies about what to include will be provided in the responses. The only
exception is security disclosures which should be sent privately.

Committers may direct you to another repository, ask for additional clarifications, and
add appropriate metadata before the issue is addressed.

## Contributions

Any change to resources in this repository must be through pull requests. This applies to all changes
to documentation, code, binary files, etc.

No pull request can be merged without being reviewed and approved.

## Validate your changes

Verify that the project is working by running `zopen build`.
For more information on using `zopen build`, look [here](###Useful Commands).

## Coding Guidelines

When contributing your changes, please follow the following coding guidelines:
* patches: patches should adhere to the coding guidelines from the original project repository. Make sure to add the original project's LICENSE file within the patches
directory.
* zopen framework files: (e.g. buildenv) - It is recommended that you follow the [Google Shell Style Guide](https://google.github.io/styleguide/shellguide.html)

If you are generating a new project, we recommend that you use `zopen generate` to create the correct zopen file and directory structure.

### Commit message

A good commit message should describe what changed and why.

It should:
  * contain a short description of the change
  * be entirely in lowercase with the exception of proper nouns, acronyms, and the words that refer to code, like function/variable names
  * be prefixed with one of the following words:
    * fix: bug fix
    * hotfix: urgent bug fix
    * feat: new or updated feature
    * docs: documentation updates
    * refactor: code refactoring (no functional change)
    * perf: performance improvement
    * test: tests and CI updates

### Developer's Certificate of Origin 1.1

<pre>
By making a contribution to this project, I certify that:

 (a) The contribution was created in whole or in part by me and I
     have the right to submit it under the open source license
     indicated in the file; or

 (b) The contribution is based upon previous work that, to the best
     of my knowledge, is covered under an appropriate open source
     license and I have the right under that license to submit that
     work with modifications, whether created in whole or in part
     by me, under the same open source license (unless I am
     permitted to submit under a different license), as indicated
     in the file; or

 (c) The contribution was provided directly to me by some other
     person who certified (a), (b) or (c) and I have not modified
     it.

 (d) I understand and agree that this project and the contribution
     are public and that a record of the contribution (including all
     personal information I submit with it, including my sign-off) is
     maintained indefinitely and may be redistributed consistent with
     this project or the open source license(s) involved.
</pre>

## Building

To begin, here an example of how to build gitport on z/OS

```
Create a dev directory under /YourUsername, e.g mkdir /YourUsername/dev

cd dev

git clone git@github.com:ZOSOpenTools/gitport.git
Note: You may need to add your SSH key to your github profile. This would be found under Settings->SSH and GPG Keys

cd gitport

Add the clang compiler to your path: export PATH="/usr/lpp/IBM/oelcpp/v2r0/bin:$PATH"

zopen build -vv

It should build successfully
```
You can also run zopen build from the root directory of the git repository you would like to build. For Boost:
```
cd ${HOME}/zopen/dev/boostport
zopen build
```
In order to view the functions that we have access to on z/OS, you can run the following command:
```
cd /usr/include
ls
```
This should list all of the available files. They can be accessed however is needed (ie. vim, cat, grep)

For example: 
```
cd /usr/include
ls
grep -r "StringToFind" .

Note, if the grep command does not work, this is an alternative:
find . -type f -exec grep -H "StringToFind" {} \;
```
Would search the current directory (and lower directories when using -r) for "StringToFind"


After running the zopen build command, there should be an error that is output if it doesn't build successfully. The error message should be printed to the terminal and should also be saved in a log. 

### Buildenv
Buildenv is the main file that will be altered in order to make build changes. There are multiple sections that each serve a different purpose.
  * The first section is before any of the functions run, and it is where all of the pro compilation changes should be made. For example, we wanted to specify which compiler to use so we added ```export ZOPEN_CONFIGURE_OPTS="--with-toolset=clang --prefix=\$ZOPEN_INSTALL_DIR"```. All of these should be clarified with ```export```.
  * The next part is the funciton ```zopen_init()```. This will run right after the default patches are applied to the build. The function is dedicated to applying the custom patches that are created using the .diff file in [General Process](#general-process). These are then applied in this function. For example, the patch application of fileunix.cpp is shown below:
   ```
    # fileunix.cpp patch -> located at ./boost/tools/build/src/engine/fileunix.cpp
     echo "In ZOPEN_INIT, applying patch to $ZOPEN_ROOT/patches/fix_fileunix"
     cd tools/build
     git apply $ZOPEN_ROOT/patches/fileunix.cpp.diff
     cd -
   ```
  *  The next funciton is ```zopen_make()``` and its purpose is to specify build changes. If you are familiar with make most of the content here should be similar. For example, we wanted to use certain flags on the compilation of our build, so we specified:
    ```
    ./b2 headers cxxflags="$CPPFLAGS $CXXFLAGS" linkflags="$LDFLAGS $LIBS"
    ```
  * The next function is ```zopen_check_results()``` which occurs post-build and clarifies how successful the build was. Currently no changes need to be made as the default for checking the build should be sufficient.
  * The next function is ```zopen_append_to_env()``` which echoes environment variables outside of PATH, MANPATH, and LIBPATH.
  * ```zopen_append_to_setup()``` will echo commands that will run when installing via setup.sh```
  * Finally, ```zopen_get_version()``` is supposed to echo the version of the library. Currently it is hardcoded but in the future it should be based off the tool/libary.


### Compiler Errors
Compiler errors can be common when porting boost since many of the libraries boost uses have functions that are not supported in z/OS. In order to check if a function is supported, you can check the list of available files using the above method.

Once you have identified a compiler error, there are a couple steps to take as to go about resolving it.

First, resolve the issue in the file itself. Go to the file where the compiler error occurs, and try to resolve it. This usually takes the form of writing a new function or using a different one that is supported by z/OS. Test this change by building again. Repeat untill the issue is resolved.

Once you have a working change, you create a patch so that the compiler error can be avoided while building on a different system.

### Useful Commands
```
zopen --help - See the list of commands
zopen list - Lists everything that has been ported
zopen build - Builds the current project
  -h - Prints the information
  -v - Run in verbose mode
  -vv - Run in very verbose mode
  -f - Force a rebuild
  -c - Clean
```

### Editing Files on z/OS

Using VIM is the quickest and easiest ways to edit files in z/OS Open Tools. VIM is the recommended text editor for z/OS and can be installed by running the following command: 
```
zopen install vim
```
After making changes to a file in the server, you can securely transfer the updated file back to your local machine and then to GitHub repository using the scp command.

Command: 
```
scp -i ~/.ssh/your_ssh_key username@server_ip:/remote/directory/updated_file.cpp /local/directory/destination_file.cpp
```
Push the changes to your GitHub repository:
```
cd /local/git/repo/directory
git add .
git commit -m "Updated file"
git push origin main
```

### Keeping Track of Error Logs

z/OS provides a way for you to visit error logs. If the build is unsuccessful, an error message will display the reason the compiler failed. The error message will contain a warning or type of error, followed by the destination of the log file created. Every unsuccessful build will create a log file that stores the output and errors encountered during build and the file is saved in boostport's directory.  
For example: 
```
***ERROR: Make (full) failed. Log: /UserName/boostport/log.DEV/20231201_162710_build.log
```
You can visit the history of your log files by navigating to your boostport directory. 
```
cd /usr/boostport/log.DEV
```
If you have vim installed, you can view the log files: 
```
vim build.log
```

### Using Dump Files

If certain exceptions occur, then the build process will create a CEE (Common Execution Environment) dump file and produce an output similar to the following:
```
CEE3204S The system detected a protection exception (System Completion Code=0C4).
    From entry point _Z11list_appendP4LISTS0_ at compile unit offset +000000002665EB28 at entry offset +0000000000000188 at address 000000002665EB28.
```

A CEE dump file is generated when the build process encounters a critical exception. The purpose of this file is to record a traceback, capturing relevant information about the state of the program at the moment the exception occurred.

To locate the dump files:
```
ls CEEDUMP.*
```

The dump file provides a traceback of function calls (and the critical exception) in reverse chronological order. Since function names in C++ are mangled, you should use a demangler such as `http://demangler.com/`: demangling `_Z11list_appendP4LISTS0_` gives `list_append(LIST*, LIST*)`.

To generate a list of files containing that function:
```
git grep --recurse-submodules list_append
```

To locate the function definition, you can use a utility called `ctags`.
First, install `ctags`:
```
zopen install ctags
```

Next, use `ctags` to generate a tags file for the source code in the current directory and its subdirectories:
```
ctags -R .
```
The tags file contains an index of functions, classes, variables, and other identifiers in the code.

Finally, use Vim to open the file containing the tag for `list_append` and place the cursor at the corresponding line:
```
vim -t list_append
```

## General Process

Firstly, there are the compiler errors when running zopen build. For these issues, it is often that a function does not exist on z/OS that is attempting to be used in the codebase. For this, there are two options. You can either find an alternative function and use that instead of the original function that does not exist on z/OS. This can be found by using the /usr/include section for what functions exist. Alternatively, you can also define the function if it doesn't exist. 

Adding the file can by done by writing a patch. This would be in the boostport/patches folder. In this folder, you can make a .diff file. For example, there is currently an execunix.cpp.diff file. In this file, you can add additional functions as needed that will be applied before the build process. This is an excerpt from the execunix.cpp.diff file:
```
+#ifdef __MVS__
+                while ( ( pid = waitpid( cmdtab[ i ].pid, &status, 0 ) ) == -1 )
+#else
                 while ( ( pid = wait4( cmdtab[ i ].pid, &status, 0, &cmd_usage ) ) == -1 )
+#endif
```
In this patch, we see that if `__MVS__` then we swap the wait4 function for an equivalent line using the waitpid function instead. It is important to note that `__MVS__` is true if the current platform is z/OS. This will be used extensively in patches. 
