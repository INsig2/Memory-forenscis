# How to create a custom Windows Volatility 2 profile

This is a tutorial that will explain how to create a custom Volatility 2 profile for ANY version of Windows, including the latest version of Win 11 22H2. Unless things change drastically, the process should work for future versions as well. 

The process was created under INsig2 class Advanced RAM forensics. The steps below don't include every possible step, and does not provide the files need, but will hopefully help investigators create the proper profile. Specific knowledge on Python will be required! 

If you are interested in the full tutorial and class that goes deeper into the RAM forensics, please see our site for more info www.insig2.com 

The process is as follows and consists of several steps and will need several things:

**Things needed:**

*   Taking the ntoskrnl.exe form the target system
    *   Located in C:\\Windows\\System32
    *   Will need this to generate vtypes
*   Pdbparse python 2 script - [https://github.com/moyix/pdbparse](https://github.com/moyix/pdbparse) 
    *   Used for generating the pdb files – symbols tables for specific windows distribution
    *   Used for parsing the pdb files
    *   Will have some dependencies for it to work properly
    *   \*Sometimes can be a pain to make it work
*   Microsoft Visual C++ Compiler for Python 2.7
    *   Needed for proper installation od compilation of pdbparse

**The process:**

*   Copy out ntoskrnl.exe form the target system
    *   Located in C:\\Windows\\System32
*   Install VCForPython27.msi 
*   Install Pdbparse python 2 script
*   Once you have everyting installed properly
    *   Copy the ntoskrnl.exe from your target system, to the examination system and run the following command
    *   `symchk.py -e <path to ntoskrnl.exe file>`
    *   `pdb_tpi_vtypes.py ntkrnlmp.pdb > <output file>.py`

The resulting .py file is actually our volatility 2 profile!

There are few more steps that wee need to do before we can use it

*   Rename the \*.py file to the corresponding Windows version with suggested format:
    *   win\<XX>\_x64\_\<XXXXXX>\_vtypes.py
        *   \<XX> 10, 11, 12?
        *   \<XXXXX> would be smart to make it specific minor Windows version e.g. 19044 or something similar - please see [https://en.wikipedia.org/wiki/List_of_Microsoft_Windows_versions](https://en.wikipedia.org/wiki/List_of_Microsoft_Windows_versions)
*   The Win\<XX>\_x64\_\<XXXXXX>\_vtypes.py goes with its friends in “...volatility-master\\volatility\\plugins\\overlays\\windows”
    *   You will see in a bit why...
*   Last thing we need is to modify the win10.py 
    *   That’s our list of win10 profiles that we see in Volatility info   
        “…\\volatility-master\\volatility\\plugins\\overlays\\windows\\win10.py“
    *   Open the file with text editor
    *   Scroll all the way to the end
    *   We will copy and paste the last class and use it as our template for the new profile
*   Last thing we need is to modify the win10.py
    *   We will modify the last class to match the profile and filename we just created

![](https://user-images.githubusercontent.com/121233698/209137348-526afe44-8d51-466a-a0c5-1ca5951b1d63.png)

*   Eg – set all to Win10 x64 19044
*   Save and verify by running in CMD
*   BE CAREFUL not to include the \*.py extension in \_md\_vtype\_module
*   Vol.py –-info and see if your new profile is there under Windows profiles

NOTE: This new profile will still be under Win10 profiles - if you are working with later versions of Win eg. 11, 12, 13 they will be nested under Win10 but we dont care! Volatiltiy will complain about not having 32bit equivalent profile, again we don't care as we don't need it. 

Happy times!
