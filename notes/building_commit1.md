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

## Warning 1: Implicit declarations
Function `memcmp` has implicit declaration in `update-cache.c`. OK we follow compiler suggestion and include it in `cache.h` because that's include in turn into `update-cache.c`.
Cleaning and building again, this warning disappears. Good.

## Warning 2: Deprecated function in libcrypto
OK, gcc is complaining about deprecated functions `SHA1_Init` etc since openssl3 release. I can't do much about it because the original code was written in 2005. Maybe these functions were deprecated at a later date (openSSL3 released in 2021). I can't address this one and will have to live with these warning because I am not sure what are the replacement functions and whether they will work as Linux intended. So in the interest of sticking with what Linus built, let's ignore these warnings. This is an issue of use of outdated library functions. Not ideal but it should work. 

##Warning 3: No return value for main()
For `init-db.c`, gcc complains that `main` should return an int but line 31 has a return statement with no value. Fair enough. why is return specified with no value? This is an error situation so 0 should not be returned. I suppose anything else is fine. But gcc is complaining so let's specify something. How about '-1'. Not sure if these return codes will get used later and I'll break something, but at least I am returning something deterministic. 
Afer cleaning and building again, this warning has disappeared.

No more addressable warnings. So that's it. That's the build. 


## The Output of build
The output of build has 7 executable files. This is another interesting observation. Instead of building a huge monolithic process, Linus is building small processes that will work together. I suppose these processes will be stiched together in some other programs or a bash script. That's a good lesson to remember. Don't build huge monoliths, but try to build small processes that do specific jobs and leave other jobs to other processes.



  
