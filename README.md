# Errors-PythonFlu-OpenFOAM-4.1
Documentation of errors encountered while updating pythonFlu to OpenFOAM-4.1


## Description about files in repository
AllwmakeOutput4 consists of logs, which were generated when we built OpenFOAM-4.1 from sources
AllwmakeOutput2 consists of logs, which were generated when we built OpenFOAM-2.1.1 from sources
makeOutput4 consists of logs, which were generated when we built managedFlu in OpenFOAM-4.1
makeOutput2 consists of logs, which were generated when we built managedFlu in OpenFOAM-2.1.1 in Ubuntu 12.04


## Erros encounterd while reviving pythonFlu
pythonFlu provides Python wrapping for OpenFOAM C++ API. It enriches the existing OpenFOAM best features, such as expressiveness and robustness, with such unique Python advantages as interactivity and automation. pythonFlu delivers unique engineering environment, where everything can be accessed and run in the same Python terms. 

For pythonFlu to be installed, one needs to mandatory install confFlu and managedFlu 
Detailed information about confFlu and managedFlu can be found [here](https://github.com/alexey4petrov/confFlu) and [here](https://github.com/asimurzin/managedFlu) 
It turns out that managedFlu and confFlu are also out of sync and one needs to change the env.sh file of managedFlu depending upon the directory where both confFlu and OpenFOAM are located. 

So before we can start work on pythonFlu we needed to make confFlu and managedFlu compitable with OpenFOAM-4.1 
As confFlu depends mostly on automake, there were no errors found while installing confFlu on ubuntu-16.04 machines which had OpenFOAM-4.1 installed 

But many errors came into picture when we tried to make managedFlu working with OpenFOAM-4.1 
As these 3 pythonFlu, confFlu, managedFlu depend on OpenFOAM-2.1.1 and also they depend on which ubuntu version you are working on. Due to minor changes in location of python2.7 shared library in ubuntu-12.04 and ubuntu 16.04 we manually had to copy the libpython-2.7.so file from another folder to the folder in which managedFlu was looking for 

But the errors did not stop there. Due to massive jump from OpenFOAM-2.1.1 to OpenFOAM-4.1 it turns out that there are some header files and source files (required to build managedFlu) which are permanantly removed or merged into some other header files or are renamed into some other files or have been located to somewhere else. Some examples of such header files are
* MRFZones.H and MRFZones.C, Present in OpenFOAM-2.1.1 src/finiteVolume/lnInclude directory
* basicPsiThermo.H, Present in OpenFOAM-2.1.1, Not present in OpenFOAM-4.1 at all
* radiationModel.H, whose location has been changed
* PorousZones.H, Not present in OpenFOAM-4.1
* turbulenceModel.H, whose location has been changed

Also, because of these missing files, the required .o files of managedFlu are building and thus the required shared library .so files are not made. And the later stages of compilation requires the libraries which were build in previous stages of compilation and because of that too it raises linking errors. 

So we tried to first debug the error regarding MRFZones.H and MRFZones.C not found. It turns out that MRFZone.H and MRFZone.C in OpenFOAM-4.1 are merged and superior versions of seperate files MRFZone.C MRFZones.C MRFZones.H MRFZone.H in OpenFOAM-2.1.1 

So we tried to work around this error using MRFZone.H of current OpenFOAM-4.1 but it seems that the constructor of that class has been greatly modified from being 1 parameter constructor to 4 parameter constructor and it was of no help. 

We also tried to find how these header files come after compiling OpenFOAM-4.1 as they were not present when we downloaded sources from github but we could not find out more from it. Though every log is present in this repository 

