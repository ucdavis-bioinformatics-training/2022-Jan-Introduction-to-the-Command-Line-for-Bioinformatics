Installing Simple Bioinformatics Software
===========================================

**RUN THESE COMMANDS FIRST!**

## For Ubuntu on Windows

	sudo apt update
	sudo apt upgrade
	sudo aptitude install gcc
	sudo aptitude install libncurses5-de
	sudo aptitude install zlib1g-dev
	sudo aptitude install libbz2-dev
	sudo aptitude install liblzma-dev
	sudo aptitude install libcurl4-gnutls-dev
	sudo aptitude install g++
	sudo aptitude install libhdf5-dev
	sudo aptitude install autoconf
	sudo aptitude install cmake


---

**1\.** Let's spend some time installing software. We will first install an Illumina read trimmer called **sickle** written by members of the Bioinformatics Core. The source code for many bioinformatics software are on github.... sickle is found on the [Bioinformatics Core github page](https://github.com/ucdavis-bioinformatics).

---

**2\.** Find sickle and navigate to the page. Click on "Clone or Download" and copy the URL. In your home directory, create a "software" directory and go into it. In order to clone (get a copy of) the repository, we need to use the "git" command. Clone the git repository in the software directory:

	cd
	mkdir software
	cd software
	git clone https://github.com/ucdavis-bioinformatics/sickle.git

git has many subcommands, but the one you will use the most (unless you are creating software) is "git clone" to get a copy of the source code for some software from github.

---

**3\.** A directory called "sickle" will be created. Go into the directory and take a look at the Makefile:

	cd sickle
	cat Makefile

---

**4\.** Take a look at the sickle github page again. Read down to "Building and Installing sickle". The "make" command looks for the file called "Makefile" for instructions on compiling the code. Run make:

	make

---

**5\.** The make command will compile the source code (using a C compiler called gcc) and create the sickle executable. In order for this to work the gcc compiler must already be installed on your system. Now you can run sickle (you need to run it by typing "./sickle" because we haven't added "." to our PATH):

	./sickle

This will give you the help page for the sickle sub-commands. Run one of the sub-commands with no options to get the help page for that sub-command:

	./sickle pe

---

**6\.** Now, let's make it so that we can run sickle from any directory. In order to do this, we need to add the full path to the sickle directory to the PATH environment variable. The PATH environment variable is a list of colon-separated directories that are searched in for anything you are trying to execute. First take a look at what the PATH variable contains:

	echo $PATH

The "$" is used to access the value of the variable. You will notice that there are a already a number of default directories. We want to add the full path to the directory where you created sickle. Use the "pwd" command to find the path:

	pwd

Then use the "export" command to add the directory to the PATH variable (replace the words below, **including the arrows**, with your path):

	export PATH=$PATH:<add the path from the pwd command here>

Notice a few things. First, in order to assign a new value to a variable, we specify the variable **without** the "$". Then, we set it equal to "$PATH" plus the new directory separated by a colon. This is so that we keep the old directories and add a new one. If we didn't add "$PATH", then the PATH variable would be overwritten, rather than added to.

---

**7\.** Now you should be able to run sickle from any directory. Go back to your home directory and try to run sickle:

	cd
	sickle
	sickle pe

Even though sickle is not in the current directory, it is run from the correct directory.

---

**8\.** There is a problem, however. The "export" command will only work for this session. If you log out and log back in, you will have to do it again. Let's make it so that the export command happens automatically when you log in. In order to do this, we need to edit the ".bash_profile" (or sometimes ".bashrc") file. Use the "nano" editor to edit the file, which is located in your home directory (if it doesn't exist, this will create it):

	nano ~/.bash_profile

Add the export line from above to the file and save it. Now, whenever you log in, the export command will automatically run.

We should also add "." to our PATH so that we can run things that are in our current directory. So edit your .bash_profile again and add "." to the PATH variable (remember to separate it with a ":"):

	export PATH=$PATH:<add the path from the pwd command here>:.

---

**9\.** Now, let's try installing something a little trickier.... an alignment format manipulation software called "samtools". First, go to the [samtools website](http://www.htslib.org). Click on the blue "Source Release Details" download button. Right click (or your equivalent) on the "samtools-1.14" link and copy the link location. Use the "wget" command to retrieve the file. So it will look like this (go back to your software directory first):

	cd ~/software
	wget https://github.com/samtools/samtools/releases/download/1.14/samtools-1.14.tar.bz2

---

**10\.** Now you should have the "samtools-1.9.tar.bz2" file in your home directory. Next use the "tar" command to extract the archive from the file:

	tar -x -v -j -f samtools-1.14.tar.bz2

The "-x" is the extract flag, "-v" is verbose informational output, "-j" specifies the format of the compression (in this case bzip2), and "-f" specifies the file. Now you should have a directory called "samtools-1.14".

---

**11\.** Go into that directory and look at the files:

	cd samtools-1.14
	ls

Take a look at the README file:

	cat README

This gives you instructions on how to install samtools, however, we're going to do something a little different. We are going to specify an installation directory. So first, let's create that directory:

	mkdir ~/software/samtools_install

This creates a new, empty "samtools_install" directory in our previously created "software" directory.

---

**12\.** Now, you will notice that the "samtools-1.14" directory has a file called "configure" in it. Whenever software has this file, you need to run it first before running "make". We are going to run it and include the directory (use your home directory) where we want the final product to go (using the \-\-prefix flag). Mac users will have a path like "/Users/joshi/software/samtools_install".:

	./configure --prefix=/home/joshi/software/samtools_install

This will scan your system and make sure it has all of the necessary components to compile samtools. Once that is done, run make:

	make

This will take a few minutes.

---

**13\.** Once the make command finishes, run this in order to install samtools in our installation directory:

	make install

Now if you go into that directory you should see two new directories, "share" and "bin". The "bin" directory contains the final executables generated by "make". The "share" directory contains manual pages for the "man" command. 

	cd ~/software/samtools_install
	ls
	ls bin

Now, as an exercise, add the correct directory to your path so that you can run samtools from anywhere.


## Using cmake

Now we will compile a piece of software using cmake. cmake is another build system (like make) that is used for compiling and installing software. It basically sets up the build environment for make so that it can easily run on multple systems. First, let's clone the 'megahit' software github repo.

	cd ~/software
	git clone https://github.com/s4hts/HTStream.git

Go into the directory and look around

	cd HTStream
	ls

Create a HTStream install directory in software. Create a directory called "build" and go into it.

	mkdir ../hts_install
	mkdir build
	cd build

From the build directory, we are going to run cmake using the CMAKE_INSTALL_PREFIX option:

	cmake -DCMAKE_INSTALL_PREFIX=/home/joshi/software/hts_install ..

What happened? This failed because you are missing a library. You should see an error something like this:

<div class="output">
CMake Error at /usr/share/cmake-3.16/Modules/FindPackageHandleStandardArgs.cmake:146 (message):
  Could NOT find Boost (missing: Boost_INCLUDE_DIR system program_options
  filesystem iostreams) (Required is at least version "1.56")
Call Stack (most recent call first):
  /usr/share/cmake-3.16/Modules/FindPackageHandleStandardArgs.cmake:393 (_FPHSA_FAILURE_MESSAGE)
  /usr/share/cmake-3.16/Modules/FindBoost.cmake:2179 (find_package_handle_standard_args)
  common/CMakeLists.txt:26 (FIND_PACKAGE)


-- Configuring incomplete, errors occurred!
See also "/home/joshi/software/HTStream/build/CMakeFiles/CMakeOutput.log".
</div>

The library that is missing is called "boost". To search for the library, you can use these commands:

For Ubuntu:

	aptitude search boost

For Macs:

	brew search boost

Find the packages you need and install them:

For Ubuntu:

	aptitude install libboost-all-dev

For Macs:

	brew install boost

Then run cmake again:

	cmake -DCMAKE_INSTALL_PREFIX=/home/joshi/software/hts_install ..

Once cmake is done, then we run 'make' and 'make install' as before:

	make
	make install