# Contributing

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

In order to view the files that we have access to on z/OS, you can run the following command:
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
```
Would search the current directory (and lower directories when using -r) for "StringToFind"


After running the zopen build command, there should be an error that is output if it doesn't build successfully. The error message should be printed to the terminal and should also be saved in a log. 

### Compiler Errors
Compiler errors can be common when porting boost since many of the libraries boost uses have functions that are not supported in z/OS. In order to check if a function is supported, you can check the list of available files using the above method.

Once you have identified a compiler error, there are a couple steps to take as to go about resolving it.

First, resolve the issue in the file itself. Go to the file where the compiler error occurs, and try to resolve it. This usually takes the form of writing a new function or using a different one that is supported by z/OS. Test this change by building again. Repeat untill the issue is resolved.

Once you have a working change, you create a patch so that the compiler error can be avoided while building on a different system.

### Keeping Track of Error Logs

z/OS provides a way for you to visit error logs. If the build is unsuccessful, an error message will display the reason the compiler failed. The error message will contain a warning or type of error, followed by the destination of the log file created. Every unsuccessful build will create a log file that stores the output and errors encountered during build and the file is saved in boostport's directory.  
For example: 
```
***ERROR: Make (full) failed. Log: /andy/boostport/log.DEV/20231201_162710_build.log
```
You can visit the history of your log files by navigating to your boostport directory. 
```
cd /usr/boostport/log.DEV
```
If you have vim installed, you can view the log files: 
```
VIM build.log
```
