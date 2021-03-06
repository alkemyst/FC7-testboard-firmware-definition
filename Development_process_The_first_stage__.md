# Development process

The first stage of the development will be the definition of the /architecture/, in the following order:
* definition of functional blocks with a coarse definition of functions
* definition of the communication between blocks
* precise definition of the blocks functions and interfaces

The second stage will be the actual development of VHDL code for the various blocks
* creation of a github repository
* creation of a vivado project importing the FC7 system firmware
* creation of (empty) blocks in the project

It should be possible to switch between alternate blocks in the same structure by switch available at compilation time. Instruction on compiling the project along with the definition of compiler switches should be provided in the README.md file, so that it is accessible from the GitHub main project page.

Contributors should always follow the git workflow, merging the main branch locally and solving conflicts before creating a pull request. Tagged versions should be used in production, even if the master branch should always be compiling, functional. No "development" branch is suggested: every contributor will manage their development independently and only request pulls for code versions which where tested locally. 