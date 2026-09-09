# How to Install SAL4LWTools (win-x64)
Installation of SAL4LWTools consists of:

    1. Extracting the contents of the .zip file to the location
       of your choice;
    2. Configuring access to the SAL4LWTools executables;
    3. Configuring access to the LWTools executables; and
    4. Testing the SAL4LWTools installation.

It is expected that the LWTools toolchain (https://www.lwtools.ca/)
is already installed on the system.

## Step 1. Extract the archive.
Extract the .zip file to the location of your choice. If you plan to build
the sample code "in place", then be sure to choose an installation directory
that is writeable by applications. Otherwise, the installation directory
does not need to be writeable by applications.

## Step 2. (Optional) Configure access to the SAL4LWTools executables.
To access SAL4LWTools from make files and build scripts without hard-coding
the SAL4LWTools installation directory, you may want to add the directory
to your system's executable search path. Alternatively, you may want to add
the environment variable SAL4LWToolsPath that points to the installation
directory, as shown below, so that build scripts can use SAL4LWToolsPath to
locate the SAL4LWTools executable. The Build.cmd scripts that accompany
the SAL samples check the SAL4LWToolsPath variable first and then fall
back to the executable search path.

Example:

    set SAL4LWToolsPath=C:\Program Files\SAL4LWTools


## Step 3. (Optional) Configure access to the LWTools executables.
If you plan to use the "assemble" and "assemble and link" features of 
SAL4LWTools to automatically executate LWASM and LWLINK after a SAL
translation, you'll need to let SAL4LWTools know where to find the LWTools
executables. You can do this directly in the translation command, or
you can either add LWTools to the execution search path or define the
LWToolsPath environment variable to point to the LWTools installation
directory, as shown below.

Example:

    set LWToolsPath=C:\Program Files\lwtools-4.24


## Step 4. Test your installation.
To test whether SAL4LWTools is properly accessible via the path or
environment variables, you can use the "testconfig" command. First, switch
to a writable directory, and then issue the testconfig command as shown
below. If everything is working correctly, it will create a subdirectory
called "test-output", containing the files produced by the testconfig
command.

You can test access to SAL4LWTools by itself, or you can test it in
combination with access to LWTools.

### Testing SAL4LWTools by itself
To test SAL4LWTools on its own without the LWTools toolchain, use the 
the testconfig command as show in this section. Use this test if you
plan to use SAL4LWTools to translate SAL files without automatically
passing the resulting assembly files to LWASM. This version of the test 
will simply use SAL4LWTools to translate one of the sample programs.

If you've added SAL4LWTools to the search path, you can test it like this.

    SAL4LWTools testconfig


If you've added the SAL4LWToolsPath variable to your environment, you can
test it like this.

    %SAL4LWToolsPath%\SAL4LWTools testconfig


### Testing SAL4LWTools and LWTools together
To test SAL4LWTools in combination with the LWTools tool chain, use the
testconfig command as shown in this section. Use this test if you plan to
have SAL4LWTools automatically pass translated SAL files to LWASM and
LWLINK. This version of the test will translate one of the sample programs
and then run LWASM to assemble the translated output and then LWLINK to link
the assembled output into a runnable CoCo executable.

If you've added SAL4LWTools to the search path, you can test it like this.

    SAL4LWTools testconfig -al


If you've added the SAL4LWToolsPath variable to your environment, you can
test it like this.

    %SAL4LWToolsPath%\SAL4LWTools testconfig -al

***end of document***