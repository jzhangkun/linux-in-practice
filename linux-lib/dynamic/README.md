# Shared Object
How to generate a shared object: (Dynamically linked object library file.) Note that this is a two step process.
- Create object code
- Create library
- Optional: create default version using a symbolic link.

## Library creation example:

```c
    gcc -Wall -fPIC -c *.c
    gcc -shared -Wl,-soname,libctest.so.1 -o libctest.so.1.0  *.o
    mv libctest.so.1.0 ./lib
    ln -sf ./lib/libctest.so.1.0 ./lib/libctest.so.1
    ln -sf ./lib/libctest.so.1.0 ./lib/libctest.so
```
This creates the library libctest.so.1.0 and symbolic links to it.

It is also valid to cascade the linkage:

    ln -sf ./lib/libctest.so.1.0 ./lib/libctest.so.1
    ln -sf ./lib/libctest.so.1  ./lib/libctest.so


If you look at the libraries in /lib/ and /usr/lib/ you will find both methodologies present. Linux developers are not consistent. What is important is that the symbolic links eventually point to an actual library.

## Compiler options:

- -Wall: include warnings. See man page for warnings specified.
- -fPIC: Compiler directive to output position independent code, a characteristic required by shared libraries. Also see "-fpic".
- -shared: Produce a shared object which can then be linked with other objects to form an executable.
- -Wl,options: Pass options to linker.
- In this example the options to be passed on to the linker are: "-soname libctest.so.1". The name passed with the "-o" option is passed to gcc.
- Option -o: Output of operation. In this case the name of the shared object to be output will be "libctest.so.1.0"

## Library Links:

- The link to /opt/lib/libctest.so allows the naming convention for the compile flag -lctest to work.
- The link to /opt/lib/libctest.so.1 allows the run time binding to work. See dependency below.

Compile main program and link with shared object library:

Compiling for runtime linking with a dynamically linked libctest.so.1.0:

```c
    gcc -Wall -I/path/to/include-files -L/path/to/libraries prog.c -lctest -o prog

Use:

    gcc -Wall -L/opt/lib prog.c -lctest -o prog
```

Where the name of the library is libctest.so. (This is why you must create the symbolic links or you will get the error "/usr/bin/ld: cannot find -lctest".)
The libraries will NOT be included in the executable but will be dynamically linked during runtime execution.
