# Building Commit 1

Let's switch directory to `src` where the files from first git commit are copied. and let's try to buildit. 

so let's run `$make` and see if we have got all the dependencies in order. 

and the build failed. 
There are lots of warnings, but build failed because of an error. So let's focus on the error first. 

## Error 1: Multiple definitions 
The linker is not happy. Looks like `read-cache.o` and `update-cache.o` have duplicate definition of they symbols `sha1_file_directory`, `active-cache`, `active-nr` and `active-alloc`. 

These are declared in `cache.h` line 61 to 63 and initialised in `read-cache.c` lines 3 to 5. 
These are not declared as `extern` in `cache.c`, so that seems to be the problem. Let's declare these as extern so the linker will look for those elsewhere and will find those in `read-cache.c`. 

let's try again. `$ make clean` and `$ make` again. This error disappears, so we are good. 

## Error 2: Undefined reference to linker symbols.
Linker is again not happy, it is looking for some symbols but cannot find those. What could be wrong?
The missing symbols are `SHA1_Init`, `SHA1_Update`, `SHA1_Final' (all crypto related) and  `inflate`, `inflateEnd`, `deflate`, `deflateBound`, and `deflateEnd` (Looks like they are compression related). 

A google search shows that I should expect to find the first set of symbols in `libcrypto` and the second set of symbols in `libz.so`. 

Running a search on the file system, we do have `libcrypto` at `/usr/lib/x86_64-linux-gnu/libcrypto.so`. 
and we do have libz at `/usr/lib/x86_64-linux-gnu/libz.so`, so why is linker complaining? 
Is it not the right version, which version linker should look at?

Oh I think its an issue with the make file. the `$(LIBS)` does not have these libraries listed.
Let's edit the make file and list these libraries in the `$(LIBS)` 

Let's edit line 11 in the Makefile to `LIBS= -lssl -lcrypto -lz` and try again. 

`$ make clean` and `$ make`.

Yay. It builds!. 
Lots of warnings, but no error. That's good progress. 


  
