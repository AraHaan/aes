
An AES (Rijndael) Implementation in C/C++ (as specified in FIPS-197)
====================================================================

Change (26/09/2018)
===================

1. Changes to test programs to allow them to be built on Linux/GCC
   (with thanks to Michael Mohr).

2. Rationalisation of the defines DLL_IMPORT, DYNAMIC_DLL and USE_DLL
   in the test code - now DLL_IMPORT and DLL_DYNAMIC_LOAD

3. Update the test_avs test to allow the testing of static, DLL and
   dynamically loaded DLL libraries.

Change (21/05/2018)
===================

1. Properly dectect presence of AESNI when using GCC (my thanks to 
   Peter Gutmann for this fix)

Changes (6/12/2016)
====================

1. Changed function definition of has_aes_ni() to has_aes_ni(void), 
   suggested by Peter Gutmann
   
2. Changed the default location for the vsyasm assembler to:
   C:\Program Files\yasm

Changes (27/09/2015)
====================

1. Added automatic dynamic table initialisation (my thanks to
   Henrik S. Gaßmann who proposed this addition).

Changes (09/09/2014)
====================

1. Added the ability to use Intel's hardware support for AES
   with GCC on Windows and Linux

Changes (01/09/2014)
====================

1. Clarify some user choices in the file aes_amd64.asm

2. Change the detection of the x86 and x86_64 processors
   in aesopt.h to allow assembler code use with GCC
    
Changes (14/11/2013)
====================

1. Added the ability to use Intel's hardware support for AES
   on Windows using Microsoft Visual Studio.

2. Added the include 'stdint.h' and used the uint<xx>_t instead
   of the old uint_<xx>t (e.g. uint_32t is now uint32_t).

3. Added a missing .text directive in aes_x86_v2.asm that caused
   runtime errors in one build configuration.

Changes (16/04/2007)
====================

These changes remove errors in the VC++ build files and add some 
improvements in file naming consitency and portability. There are
no changes to overcome reported bugs in the code.

1. gen_tabs() has been renamed to aes_init() to better decribe its
   function to those not familiar with AES internals.

2. via_ace.h has been renamed to aes_via_ace.h.

3. Minor changes have been made to aestab.h and aestab.c to enable
   all the code to be compiled in either C or C++.
   
4. The code for detecting memory alignment in aesmdoes.c has been
   simplified and a new routine has been added:
   
       aes_test_alignment_detection()
   
   to check that the aligment test is likely to be correct.

5. The addition of support for Structured Exception Handling (SEH) 
   to YASM (well done Peter and Michael!) has allowed the AMD64 
   x64 assembler code to be changed to comply with SEH requriements.
       
6. Corrections to build files (for win32 debug build).

Overview
========

This code implements AES for both 32 and 64 bit systems with optional
assembler support for x86 and AMD64/EM64T (but optimised for AMD64).

The basic AES source code files are as follows:

aes.h           the header file needed to use AES in C
aescpp.h        the header file required with to use AES in C++
aesopt.h        the header file for setting options (and some common code)
aestab.h        the header file for the AES table declaration
aescrypt.c      the main C source code file for encryption and decryption
aeskey.c        the main C source code file for the key schedule
aestab.c        the main file for the AES tables
brg_types.h     a header defining some standard types and DLL defines
brg_endian.h    a header containing code to detect or define endianness
aes_x86_v1.asm  x86 assembler (YASM) alternative to aescrypt.c using
                large tables
aes_x86_v2.asm  x86 assembler (YASM) alternative to aescrypt.c using
                compressed tables
aes_amd64.asm   AMD64 assembler (YASM) alternative to aescrypt.c using
                compressed tables

In addition AES modes are implemented in the files:

aes_modes.c     AES modes with optional support for VIA ACE detection and use
aes_via_ace.h   the header file for VIA ACE support

and Intel hardware support for AES (AES_NI) is implemented in the files

aes_ni.h        defines for AES_NI implementation
aes_ni.c        the AES_NI implementation

Other associated files for testing and support are:

aesaux.h        header for auxilliary routines for testsing
aesaux.c        auxilliary routines for testsingt
aestst.h        header file for setting the testing environment
rdtsc.h         a header file that provides access to the Time Stamp Counter
aestst.c        a simple test program for quick tests of the AES code
aesgav.c        a program to generate and verify the test vector files
aesrav.c        a program to verify output against the test vector files
aestmr.c        a program to time the code on x86 systems
modetest.c      a program to test the AES modes support
vbxam.doc       a demonstration of AES DLL use from Visual Basic in Microsoft Word
vb.txt          Visual Basic code from the above example (win32 only)
aesxam.c        an example of AES use
tablegen.c      a program to generate a simplified 'aestab.c' file for
                use with compilers that find aestab.c too complex
yasm.rules      the YASM build rules file for Microsoft Visual Studio 2005
via_ace.txt     describes support for the VIA ACE cryptography engine
aes.txt         this file

Building The AES Libraries
--------------------------

A. Versions
-----------

The code can be used to build static and dynamic libraries, each in five
versions:

 Key scheduling code in C, encrypt/decrypt in:

    C           C source code                        (win32 and x64)
    ASM_X86_V1C large table x86 assembler code       (win32)
    ASM_X86_V2C compressed table x86 assembler code  (win32)
    ASM_AMD64   compressed table x64 assembler code  (x64)
 
 Key scheduling and encrypt/decrypt code in assembler:
 
    ASM_X86_V2  compressed table x86 assembler       (win32)

The C version can be compiled for Win32 or x64 whereas the x86 and x64 
assembler versions are for Win32 and x64 respectively. 

If Intel's hardware support for AES (AES_NI) is available, it can be used
with either the C or the ASM_AMD64 version. If ASM_AMD64 is to be used, it
is important that the define USE_INTEL_AES_IF_PRESENT in asm_amd64.asm is
set to the same value as it has in aesopt.h

B. YASM
-------

If you wish to use the x86 assembler files you will also need the YASM open
source x86 assembler (r1331 or later) for Windows which can be obtained from:

    http://www.tortall.net/projects/yasm/

This assembler (vsyasm.exe) should be placed in the directory:

    C:\Program Files\yasm

C. Configuration
----------------

The following configurations are available as projects for Visual Studio
but the following descriptions should allow them to be built in other x86
environments

    lib_generic_c       Win32 and x64
        headers:        aes.h, aesopt.h, aestab.h, brg_endian.h, tdefs.h
		                (+ aes_ni.h for AES_NI)
        C source:       aescrypt.c, aeskey.c, aestab.c, aes_modes.c
		                (+ aes_ni.c for AES_NI)
        defines

    dll_generic_c       Win32 and x64
        headers:        aes.h, aesopt.h, aestab.h, brg_endian.h, tdefs.h
		                (+ aes_ni.h for AES_NI)
        C source:       aescrypt.c, aeskey.c, aestab.c, aes_modes.c
		                (+ aes_ni.c for AES_NI)
        defines         DLL_EXPORT

    lib_asm_x86_v1c     Win32
        headers:        aes.h, aesopt.h, aestab.h, brg_endian.h, tdefs.h
        C source:       aeskey.c, aestab.c, aes_modes.c
        x86 assembler:  aes_x86_v1.asm
        defines         ASM_X86_V1C (set for C and assembler files)
    
	dll_asm_x86_v1c Win32
        headers:        aes.h, aesopt.h, aestab.h, brg_endian.h, tdefs.h
        C source:       aeskey.c, aestab.c, aes_modes.c
        x86 assembler:  aes_x86_v1.asm
        defines         DLL_EXPORT, ASM_X86_V1C (set for C and assembler files)

    lib_asm_x86_v2c     Win32
        headers:        aes.h, aesopt.h, aestab.h, brg_endian.h, tdefs.h
        C source:       aeskey.c, aestab.c, aes_modes.c
        x86 assembler:  aes_x86_v2.asm
        defines         ASM_X86_V2C (set for C and assembler files)
    
	dll_asm_x86_v2c     Win32
        headers:        aes.h, aesopt.h, aestab.h, brg_endian.h, tdefs.h
        C source:       aeskey.c, aestab.c, aes_modes.c
        x86 assembler:  aes_x86_v1.asm
        defines          DLL_EXPORT, ASM_X86_V2C (set for C and assembler files)

    lib_asm_x86_v2      Win32
        headers:        aes.h, aesopt.h, aestab.h, brg_endian.h, tdefs.h
        C source:       aes_modes.c 
        x86 assembler:  aes_x86_v1.asm
        defines         ASM_X86_V2 (set for C and assembler files)
    
	dll_asm_x86_v2  Win32
        headers:        aes.h, aesopt.h, aestab.h, brg_endian.h, tdefs.h
        C source:       aes_modes.c
        x86 assembler:  aes_x86_v1.asm
        defines         DLL_EXPORT, ASM_AMD64_C (set for C and assembler files)

    lib_asm_amd64_c     x64
        headers:        aes.h, aesopt.h, aestab.h, brg_endian.h, tdefs.h
		                (+ aes_ni.h for AES_NI)
        C source:       aeskey.c, aestab.c, aes_modes.c (+ aes_ni.c for AES_NI)
        x86 assembler:  aes_amd64.asm
        defines         ASM_AMD64_C (set for C and assembler files)
    
	dll_asm_amd64_c     x64
        headers:        aes.h, aesopt.h, aestab.h, brg_endian.h, tdefs.h
		                (+ aes_ni.h for AES_NI)
        C source:       aeskey.c, aestab.c, aes_modes.c (+ aes_ni.c for AES_NI)
        x86 assembler:  aes_amd64.asm
        defines         DLL_EXPORT, ASM_AMD64_C (set for C and assembler files)

Notes:

ASM_X86_V1C is defined if using the version 1 assembler code (aescrypt1.asm).
            The defines in the assember file must match those in aes.h and
            aesopt.h).  Also remember to include/exclude the right assembler
            and C files in the build to avoid undefined or multiply defined
            symbols - include aes_x86_v1.asm and exclude aescrypt.c

ASM_X86_V2  is defined if using the version 2 assembler code (aes_x86_v2.asm).
            This version provides a full, self contained assembler version
            and does not use any C source code files except for the mutiple
            block encryption modes that are provided by aes_modes.c. The define
            ASM_X86_V2 must be set on the YASM command line (or in aes_x86_v2.asm)
            to use this version and all C files except aec_modes.c and, for the
            DLL build, aestab.c must be excluded from the build.

ASM_X86_V2C is defined when using the version 2 assembler code (aes_x86_v2.asm)
            with faster key scheduling provided by the in C code (the options in
            the assember file must match those in aes.h and aesopt.h).  In this
            case aeskey.c and aestab.c are needed with aes_x86_v2.asm and the
            define ASM_X86_V2C must be set for both the C files and for
            aes_x86_v2.asm in the build commands(or in aesopt.h and aes_x86_v2.asm).
            Include aes_x86_v2.asm, aeskey.c and aestab.c, exclude aescrypt.c for
            this option.

ASM_AMD64_C is defined when using the AMD64 assembly code because the C key
            scheduling is used in this case.

DLL_EXPORT  must be defined to generate the DLL version of the code and
            to run tests on it

DLL_IMPORT  must be defined to use the DLL version of the code in an
            application program

Directories the paths for the various directories for test vector input and
            output have to be set in aestst.h

VIA ACE     see the via_ace.txt for this item

Static      The static libraries are named:
Libraries
                aes_lib_generic_c.lib
                aes_lib_asm_x86_v1c.lib
                aes_lib_asm_x86_v2.lib
                aes_lib_asm_x86_v2c.lib
                aes_lib_asm_amd64_c.lib

            and placed in one of the the directories:

                lib\win32\release\
                lib\win32\debug\
                lib\x64\release\
                lib\x64\debug\

            in the aes root directory depending on the platform(win32 or
            x64) and the build (release or debug). After any of these is
            built it is then copied into the aes\lib directory, which is
			the library location that is subsequently used for testing. 
			Hence testing is always for the last static library built.

Dynamic     These libraries are named:
Libraries
                aes_lib_generic_c.dll
                aes_lib_asm_x86_v1c.dll
                aes_lib_asm_x86_v2.dll
                aes_lib_asm_x86_v2c.dll
                aes_lib_asm_amd64_c.dll

            and placed in one of the the directories:

                dll\win32\release\
                dll\win32\debug\
                dll\x64\release\
                dll\x64\debug\

            in the aes root directory depending on the platform(win32 or
            x64) and the build (release or debug).  Each DLL library:

                aes_<ext>.dll

            has three associated files:

                aes_dll_<ext>.lib   the library file for implicit linking
                aes_dll_<ext>.exp   the exports file
                aes_dll_<ext>.pdb   the symbol file

            After any DLL is built it and its three related files are then
            copied to the aes\dll directory, which is the library location
			used in subsequent testing.  Hence testing is always for the 
			last DLL built.
			
D. Testing
----------

These tests require that the test vector files are placed in the 'testvals' 
subdirectory.  If the AES Algorithm Validation Suite tests are used then
the *.fax files need to be put in the 'testvals\fax' subdirectory.  This is
covered in more detail below.

The projects test_lib and time_lib are used to test and time the last static
library built. They use the files:

    test_lib:       Win32 (x64 for the C and AMD64 versions)
        headers:    aes.h, aescpp.h, brg_types.h, aesaux.h and aestst.h
        C source:   aesaux.c, aesrav.c
        defines:

    time_lib:       Win32 (x64 for the C and AMD64 versions)
        headers:    aes.h, aescpp.h, brg_types.h, aesaux.h, aestst.h and rdtsc.h
        C source:   aesaux.c, aestmr.c
        defines:

The projects test_dll and time_dll are used to test and time the last DLL
built.  These use the files:

    test_dll:       Win32 (x64 for the C and AMD64 versions)
        headers:    aes.h, aescpp.h, brg_types.h, aesaux.h and aestst.h
        C source:   aesaux.c, aesrav.c
        defines:    DLL_IMPORT

    time_dll:       Win32 (x64 for the C and AMD64 versions)
        headers:    aes.h, aescpp.h, brg_types.h, aesaux.h aestst.h and rdtsc.h
        C source:   aesaux.c, aestmr.c
        defines:    DLL_IMPORT

and default to linkingto with the AES DLL using dynamic (run-time) linking.  Implicit
linking can be used by adding the lib file associated with the AES DLL (in the aes\dll
sub-directory) to the build (under project Properties|Linker in Visual Studio) and 
removing the DLL_DYNAMIC_LOAD define (under project Properties|C/C++|Preprocessor).

0  Link is linked into this project and the symbol
DLL_DYNAMIC_LOAD is left undefined, then implicit linking will be used

The above tests take command line arguments that determine which test are run
as follows:

    test_lib /t:[knec] /k:[468]
    test_dll /t:[knec] /k:[468]

where the symbols in square brackets can be used in any combination (without
the brackets) and have the following meanings:

        /t:[knec]   selects which tests are used
        /k:[468]    selects the key lengths used
        /c          compares output with reference (see later)

        k: generate ECB Known Answer Test files
        n: generate ECB Known Answer Test files (new)
        e: generate ECB Monte Carlo Test files
        c: generate CBC Monte Carlo Test files

and the characters giving the lengths are digits representing the key lengths
in 32-bit units (4, 6, 8 for lengths of 128, 192 or 256 bits respectively).

The project test_modes tests the AES modes.  It uses the files:

    test_modes:     Win32 or x64
        headers:    aes.h, aescpp.h, brg_types.h, aesaux,h and aestst.h
        C source:   aesaux.c, modetest.c
        defines:    none for static library test, DLL_IMPORT for DLL test

which again links to the last library built.

E. Other Applications
---------------------

These are:

    gen_tests       builds the test_vector files. The commad line is
                        gen_tests /t:knec /k:468 /c
                    as described earlier
                    
    test_aes_avs    run the AES Algorithm Validation Suite tests for
                    ECB, CBC, CFB and OFB modes

    gen_tables      builds a simple version of aes_tab.c (in aestab2.c)
                    for compilers that cannot handle the normal version
    aes_example     provides an example of AES use

These applications are linked to the last static library built or, if
DLL_IMPORT is defined during compilation, to the last DLL built.

F. Use of the VIA ACE Cryptography Engine (x86 only)
----------------------------------------------------

The use of the code with the VIA ACE cryptography engine in described in the
file via_ace.txt. In outline aes_modes.c is used and USE_VIA_ACE_IF_PRESENT
is defined either in section 2 of aesopt.h or as a compilation option in Visual
Studio. If in addition ASSUME_VIA_ACE_PRESENT is also defined then all normal
AES code will be removed if not needed to support VIA ACE use.  If VIA ACE
support is needed and AES assembler is being used only the ASM_X86_V1C and
ASM_X86_V2C versions should be used since ASM_X86_V2 and ASM_AMD64 do not
support the VIA ACE engine.

G. The AES Test Vector Files
----------------------------

These files fall in the following groups (where <nn> is a two digit
number):

1. ecbvk<nn>.txt  ECB vectors with variable key
2. ecbvt<nn>.txt  ECB vectors with variable text
3. ecbnk<nn>.txt  new ECB vectors with variable key
4. ecbnt<nn>.txt  new ECB vectors with variable text
5. ecbme<nn>.txt  ECB monte carlo encryption test vectors
6. ecbmd<nn>.txt  ECB monte carlo decryption test vectors
7. cbcme<nn>.txt  CBC monte carlo encryption test vectors
8. cbcmd<nn>.txt  CBC monte carlo decryption test vectors

The first digit of the numeric suffix on the filename gives the block size
in 32 bit units and the second numeric digit gives the key size. For example,
the file ecbvk44.txt provides the test vectors for ECB encryption with a 128
bit block size and a 128 bit key size. The test routines expect to find these
files in the 'testvals' subdirectory within the aes root directory. The
'outvals' subdirectory is used for outputs that are compared with the files
in 'testvals'. Note that the monte carlo test vectors are the result of
applying AES iteratively 10000 times, not just once.

The AES Algorithm Validation Suite tests can be run for ECB, CBC, CFB and 
OFB modes (CFB1 and CFB8 are not implemented).  The test routine uses the 
*.fax test files, which should be placed in the 'testvals\fax' subdirectory.

H. The Basic AES Calling Interface
----------------------------------

The basic AES code keeps its state in a context, there being different 
contexts for encryption and decryption:

    aes_encrypt_ctx
    aes_decrypt_ctx
    
The AES code is initialised with the call

    aes_init(void)
    
although this is only essential if the option to generate the AES tables at 
run-time has been set in the options (i.e.fixed tables are not being used).
    
The AES encryption key is set by one of the calls:
 
    aes_encrypt_key128(const unsigned char *key, aes_encrypt_ctx cx[1])
    aes_encrypt_key192(const unsigned char *key, aes_encrypt_ctx cx[1])
    aes_encrypt_key256(const unsigned char *key, aes_encrypt_ctx cx[1])

or by:

    aes_encrypt_key(const unsigned char *key, int key_len, 
                                                aes_encrypt_ctx cx[1])

where the key length is set by 'key_len', which can be the length in bits 
or bytes.  

Similarly, the AES decryption key is set by one of:

    aes_decrypt_key128(const unsigned char *key, aes_decrypt_ctx cx[1])
    aes_decrypt_key192(const unsigned char *key, aes_decrypt_ctx cx[1])
    aes_decrypt_key256(const unsigned char *key, aes_decrypt_ctx cx[1])

or by:

    aes_decrypt_key(const unsigned char *key, int key_len, 
                                                aes_decrypt_ctx cx[1])
 
Encryption and decryption for a single 16 byte block is then achieved using:

    aes_encrypt(const unsigned char *in, unsigned char *out, 
                                            const aes_encrypt_ctx cx[1])
    aes_decrypt(const unsigned char *in, unsigned char *out, 
                                            const aes_decrypt_ctx cx[1])
                                            
The above subroutines return a value of EXIT_SUCCESS or EXIT_FAILURE 
depending on whether the operation succeeded or failed.
 
I. The Calling Interface for the AES Modes
------------------------------------------

The subroutines for the AES modes, ECB, CBC, CFB, OFB and CTR, each process
blocks of variable length and can also be called several times to complete 
single mode operations incrementally on long messages (or those messages,
not all of which are available at the same time).  The calls:

    aes_ecb_encrypt(const unsigned char *ibuf, unsigned char *obuf,
                    int len, const aes_encrypt_ctx cx[1])

    aes_ecb_decrypt(const unsigned char *ibuf, unsigned char *obuf,
                    int len, const aes_decrypt_ctx cx[1])

for ECB operations and those for CBC:

    aes_cbc_encrypt(const unsigned char *ibuf, unsigned char *obuf,
                    int len, unsigned char *iv, const aes_encrypt_ctx cx[1])

    aes_cbc_decrypt(const unsigned char *ibuf, unsigned char *obuf,
                    int len, unsigned char *iv, const aes_decrypt_ctx cx[1])
 
can only process blocks whose lengths are multiples of 16 bytes but the calls 
for CFB, OFB and CTR mode operations:

    aes_cfb_encrypt(const unsigned char *ibuf, unsigned char *obuf,
                    int len, unsigned char *iv, aes_encrypt_ctx cx[1])

    aes_cfb_decrypt(const unsigned char *ibuf, unsigned char *obuf,
                    int len, unsigned char *iv, aes_encrypt_ctx cx[1])

    aes_ofb_encrypt(const unsigned char *ibuf, unsigned char *obuf,
                    int len, unsigned char *iv, aes_encrypt_ctx cx[1])

    aes_ofb_decrypt(const unsigned char *ibuf, unsigned char *obuf,
                    int len, unsigned char *iv, aes_encrypt_ctx cx[1])

    aes_ctr_encrypt(const unsigned char *ibuf, unsigned char *obuf,
            int len, unsigned char *cbuf, cbuf_inc ctr_inc, aes_encrypt_ctx cx[1])

    aes_ctr_decrypt(const unsigned char *ibuf, unsigned char *obuf,
            int len, unsigned char *cbuf, cbuf_inc ctr_inc, aes_encrypt_ctx cx[1])

can process blocks of any length.  Note also that CFB, OFB and CTR mode calls only
use AES encryption contexts even during decryption operations.

The calls CTR mode operations use a buffer (cbuf) which holds the counter value
together with a function parameter:

    void cbuf_inc(unsigned char *cbuf);

that is ued to update the counter value after each 16 byte AES operation. The 
counter buffer is updated appropriately to allow for incremental operations.

Please note the following IMPORTANT points about the AES mode subroutines:

    1. All modes are reset when a new AES key is set.
    
    2. Incremental calls to the different modes cannot 
       be mixed. If a change of mode is needed a new 
       key must be set or a reset must be issued (see 
       below).
       
    3. For modes with IVs, the IV value is an input AND
       an output since it is updated after each call to 
       the value needed for any subsequent incremental
       call(s). If the mode is reset, the IV hence has
       to be set (or reset) as well.
       
    4. ECB operations must be multiples of 16 bytes
       but do not need to be reset for new operations.
       
    5. CBC operations must also be multiples of 16 
       bytes and are reset for a new operation by 
       setting the IV.
       
    6. CFB, OFB and CTR mode must be reset by setting 
       a new IV value AND by calling:
       
           aes_mode_reset(aes_encrypt_ctx cx[1])
           
       For CTR mode the cbuf value also has to be reset.
       
    7. CFB, OFB and CTR modes only use AES encryption 
       operations and contexts and do not need AES
       decryption operations.
       
    8. AES keys remain valid across resets and changes
       of mode (but encryption and decryption keys must 
       both be set if they are needed).  
       
   Brian Gladman  26/09/2018
 
