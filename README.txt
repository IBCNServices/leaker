Leaker.c - the Leaker memory leak detection package

Version 0.1 - 2011-10-15
	-Initial public release

Description
-----------

Leaker is a simple memory leak detector for C/C++.  It tracks where and how you allocate and deallocate your memory.  Memory not deallocated at the end of execution is reported.  In addition, Leaker tries to detect and report other errors, such as deallocating pointers that have not been already allocated, writing off the end of arrays, and using incompatible allocators and deallocators.

Leaker consists of two files – Leaker.h and Leaker.cpp.  To use, copy both to the directory with the rest of your code.  At the top of each .c or .cpp file in your program, include the Leaker.h header file.  Then add the leaker.cpp file to your project and build.  When you run your program, errors will be report to standard error (stderr) prefixed with the string “LEAKER”.  When your program exits, a listing of all leaked allocations will be displayed.

Note that if the project you’re working on is C only, you can rename Leaker.cpp to Leaker.c.

Example
------

Suppose you have written a program consisting of 2 files.
---------
file1.cpp:

#include <stdlib.h>

char *printHello(void);

int main(int argc, char *argv[])
{
  char *s;
  s = printHello();
  free(s);
  return 0;
}
---------
file2.cpp:

#include <stdio.h>
#include <stdlib.h>
#include <string.h>

char *printHello(void)
{
  const char *s = "Hello world!\n";
  char *str = new char[strlen(s)];
  strcpy(str, s);
  puts(s);
  delete s;
  return str;
}
To use Leaker, simple copy leaker.cpp and leaker.h to the directory, add #include "leaker.h" to the top of each file, and if you're compiling with g++, instead of g++ file1.cpp file2.cpp -o program, use g++ file1.cpp file2.cpp leaker.cpp -o program.

In other words:

$ g++ file1.cpp file2.cpp leaker.cpp -o program
$ ./program

For your output you will see:

$ ./program
Hello world!

LEAKER: test2.cpp:printHello():12 delete error: pointer was not allocated!

LEAKER: test1.cpp:main():10 checking error: wrote off end of memory allocated at test2.cpp:printHello():9.

LEAKER: test1.cpp:main():10 mismatch error: memory allocated at test2.cpp:printHello():9 with new[], deallocated with free.

LEAKER: errors found!
Mismatches: 1 allocation/deallocations don't match.
Overflows: 1 allocations overflowed (wrote off end).
Bad deallocs: 1 attempts made to deallocate unallocated pointers. 

$

More information
----------------

See <http://left404.com/leaker/> for the most current version and more information.

This program is copyright 2011 by it author, Dara Hazeghi.

This program is distributed under the terms of the GNU GPL version 2.
