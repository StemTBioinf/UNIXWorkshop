## Basic UNIX/Linux and compute cluster usage
Shamit Soneji and Stefan Lang

## The LSENS platform
The platform is composed of three parts:
- The login node (front-end)
- The blades (where computation is done)
- Data servers (where data is stored)

The aim of today is to make sure you understand how all these components work and how to use them optimally.

## Using UNIX and the Lunarc LSEN HPC system

The purpose of this workshop is to get you familiar with the UNIX environment and using the Lunarc LSENS system for high-performance computing. By the time you are done here, you should be able to do the following:

- Log into Lunarc using the Thinlinc client to enter the Lunarc desktop environment
- Navigate the file structure and move files/folders around.
- Queue up a job to run on the compute nodes (a job for example could be a sequence alignment from an NGS run)
- More complicated bash usage with grep, awk, find, pipes and a tiny bit of bash scripting (this is optional/for further study).

## Get your computer ready!

Security is a high priority on Aurora LSENS2 and this starts with the server rejecting connections from all but the BMC network. This means you can only use LSENS2 from within the BMC network and the VPN you installed makes this possible (even from outside the BMC).

So fire up your VPN client (Forticlient of Shrewsoft) and **login using your lucat ID**.

## The Thinlinc Interface

Open up Thinlinc and login **using your lunarc credentials**. The server details are:

- Server: aurora-ls2.lunarc.lu.se
- Username: your aurora username
- Password: your aurora password

One time password:
The login will prompt you for a one-time passcode which is created on your phone using the 'Pocket Pass' app. Open the app and enter the 4 digit code that you set, and then enter the 6 digit number shown. You will then be logged into Aurora (the front-end). Repeat the process if it doesn't work.

You will find yourself in the Linux system, and the window manager is based on [MATE Desktop](https://mate-desktop.org/). It looks like most other operating systems with a menu etc. Take a moment to look around.

Most scientific programs for Bioinformaticians come without a graphical user interface (GUI) and therefore the 'Terminal' is our most used tool. 'Terminal' is so important that the MATE desktop has a direct link to it in the top panel between the file explorer and the web browser icon.

Open a terminal.

### Basic UNIX file structure

To know where you are right now do:
```
pwd
```

To make a new folder do:
```
mkdir NewTestFolder
```

To make a new empty file do:
```
touch NewTestFile
```

To list all files and folders do:
```
ls
```

To copy the file you have made into the folder you made do:
```
cp NewTestFile NewTestFolder/NewTestFile_copy
```

To go into the new folder do:
```
cd NewTestFolder
```

or to use the full path:
```
cd /home/username/NewTestFolder
```

Oops - this did not work - right? You need to change the 'username' to your actual user name.

You do not know your username?
```
whoami
```

Will tell you your username and
```
me=`whoami`
``` 

will store this user name in a BASH variable that we can use now.
```
cd /home/$me/NewTestFolder
```

To rename a file use the move command `mv` (yes, it's strange):
```
mv NewTestFile_copy NewTestFile_copy_version2
```

To actually move the file one level up do:
```
mv NewTestFile_copy_version2 ../
```

To go back one level and meet your file do:
```
cd ../
```

To delete the file you just moved do:
```
rm NewTestFile_copy_version2
```

To remove the folder you made do:
```
rm -R NewTestFolder
```

### Data storage
All user data should be stored on the data server - not in your home folder (your initial starting place when you login). The home folder is on the front-end that doesn't have a lot of space, which is why big data must be kept on the data servers.

Your space on LSENS2 is kept at `/projects/fs1/username/` and you get there by doing:

```
ls ~
me=`whoami`
cd /projects/fs1/$me/
```

All steps explained:
- **'~'** is an inbuilt variable that points to your home directory.
- **'me=`whoami`'** creates a local variable with the output of the whoami (speak 'who am I') program which returns our username (stefanl for me).
- **ls /projects/fs1/$me/** simply lists the contents of the folder e.g. /projects/fs1/stefanl


### Create a symbolic link to the data folder in your home directory.

Typing `/projects/fs1/$me/` everytime you want to go there is a pain, and we can get rid of this need by creating a symbolic link to your data folder in your home directory.

In the Terminal use the 'ln' command to create a link from '/projects/fs1/username/no_backup' to '~/NAS'. Do this by:

```
ln -s /projects/fs1/$me/ ~/NAS
```

If you now type `ls` you will see the `NAS` folder there. You can now do:
```
cd ~/NAS
```
Rather than having to do:
```
cd /projects/fs1/username
```
Much nicer!

## Common programs

Every Linux/Unix installation has a basic set of tools installed that are extremely helpful for the daily work. Today we already used `cd` and `ln` which moves between directories respectively links files/directories to other locations.

Other very commonly used programs are `ls`, `touch`, `cp`, `mkdir`, `mv`, `rm`, `echo`, `cat`, `cut`, `head`, `tail`, `grep`, `wc`, `tar`, `gzip`, `gunzip` and [here](https://www.tutorialspoint.com/unix_commands/index.html) for others.

You can read up on what program does and how it works using `man <program>`, for example:

```
man whoiam
```

Hit `q` to quit.

All well programmed command line tools do also report a basic help if you use them in the wrong way - like without options at all. Try it:

```
mkdir
```

```
mkdir --help
```

### Creating files / directories

Go to your data server folder (if you aren't already there):
```
cd ~/NAS
```
Create a folder with the name 'TestFileCreation':
```
mkdir TestFileCreation
```
Go into the folder:
```
cd TestFileCreation
```
Then create a file named README.txt in the folder. The file should contain the string 'Here we play with files and folders': 
```
echo 'Here we play with files and folders' > README.txt
```
To look at the contents of a file from terminal you can use the `more` command:
```
more README.txt
```

## Software installed on aurora

People use different versions of the same program, which means these programs cannot be loaded and ready to go. They need to be activated, and Aurora uses the [module system](https://lunarc-documentation.readthedocs.io/en/latest/aurora_modules/) to do this. For example, lets say that we want to use a program called `git`.

To add 'git' to your session we need to find the available versions of git:

```
module spider git
```
When we have decided which version we want we call `module spider` on the specific version to see what requirements it has:
```
module spider git/2.14.1
```
You will see that it also needs `GCCcore/7.3.0` (a dependency). To load these modules so you can use them do:
```
module load GCCcore/6.4.0 git/2.14.1
```
`git` will now be available to use for this session, and you can check this by calling:
```
git
```

<details><summary>Excercise: Try to load `samtools` version 1.7  into the session (we will use this later)</summary>
<pre><code class="bash">module spider samtools # shows which R versions are there
module spider SAMtools/1.7  # what samtools depends on
module load GCC/6.4.0-2.28  OpenMPI/2.1.2 SAMtools/1.7
</code></pre>
</p>
</details>


## Usage of the compute nodes

The Thininc client connects you to our front-end. This is ONE login computer for all of you, so do NOT run computing intensive tasks there - NEVER EVER. Instead you should use our blades to run compute heavy workloads.

Aurora uses the [SLURM system](https://lunarc-documentation.readthedocs.io/en/latest/batch_system/) to manage the job queue that distributes jobs to the compute nodes.

Lets get a small unsorted BAM file from the temp folder to work on:
```
cp /tmp/UnsortedTestFile.bam ~/NAS/TestFileCreation
``` 

Make sure you are at that location and see the file:
```
cd ~/NAS/TestFileCreation
ls
```
You should see `UnsortedTestFile.bam`.

### Sorting a BAM file using the blades

When certain jobs are run such as sequence alignment they create temporary files which are then removed/merged before the final output files are made. WE DO NOT WANT TEMPORARY FILES BEING WRITTEN TO THE STORAGE SERVERS! It creates unwanted and slow network traffic, but rather we want these files to be created on the blades where the computation is being run which is much faster.

To send a job to the queue we need to make a SLURM script that tells the blades which programs to load and what to do on which files. In our example we are going to sort a small BAM file. To do this we will use the samtools program you learned how to load earlier.

First, copy and example script from the temp folder:
```
cp /tmp/sortbam.sh .
```

Then open the script in an editor by calling:

```
gedit sortbam.sh
```

You will see the following:

```
#! /bin/bash
#SBATCH -A lsens2018-3-6 # the ID of our Aurora project
#SBATCH -n 1 # how many processor cores to use
#SBATCH -N 1 # how many processors to use (always use 1 here unless you know what you are doing)
#SBATCH -t 00:20:00 # kill the job after ths hh::mm::ss time
#SBATCH -J 'username_sort' # name of the job
#SBATCH -o 'username_sort%j.out' # stdout log file
#SBATCH -e 'username_sort%j.err' # stderr log file
#SBATCH -p dell # which partition to use
module load GCC/6.4.0-2.28  OpenMPI/2.1.2 SAMtools/1.7
samtools sort -T $SNIC_TMP UnsortedTestFile.bam > SortedTestFile.bam 
```

Change the file to replace `username` with yout actual username and then save the file. Now try to queue it up using sbatch:

```
sbatch sortbam.sh
```
You can see all the jobs on the queue using:
```
squeue
```

Your job will run, and the file SortedTestFile.bam will appear in time.

## What was `-T $SNIC_TMP` for?

Lunarc has created an environment variable named $SNIC_TMP which allows you to point out the temp folder to programs that can use one.

## Additional info: SFTP - Getting data into and out of the secure area

Data can only be put in or copied out using the inbox/outbox system which has been put in place as a security feature.

For this you will need to open and Terminal if you are on a Mac (Applications > Utilities > Terminal) and if you are on Windows 10 use WinSCP.

For terminal users the command you need to connect is:

```
sftp -P 22220 username@aurora-ls2.lunarc.lu.se
```

When you are in you go to your outbox/inbox as needed:

```
cd inbox/username
or
cd outbox/username
```

To put a file in the inbox use:
```
put filename
```

To get a file from the outbox do:
```
get filename
```

If you need to upload more than one file then zip them up first before you do so.

## Advanced terminal usage

This part of the course is optional if we have enough time. 

This part can be called advanced and will take way more concentration that the previous part.

Try to use e.g. google to answer your questions first.

If we are out of time you are welcome to try it on your own and drop by if there are questions.


### Extract information from a file

On aurora we store genome information in a special folder that is readable by all users:

```
ls /projects/fs1/common/genome/lunarc/
# e.g. gencode information mv19
ls -lh /projects/fs1/common/genome/lunarc/genomes/mouse/GRCm38.p6/gencode.vM19.chr_patch_hapl_scaff.annotation.gtf
```

These folder paths get very long so we recommend you create symbolic links again:

Link the /projects/fs1/common/genome/lunarc folder to your home directory:

```
ln -s /projects/fs1/common/genome/lunarc/ ~/lunarc
```
This will make the data storage path available as ~/lunarc

Now get some information about the gencode.vM19.chr_patch_hapl_scaff.annotation.gtf file using `ls`, `wc` and `head`.

```
ls -lh ~/lunarc/genomes/mouse/GRCm38.p6/gencode.vM19.chr_patch_hapl_scaff.annotation.gtf
wc -l ~/lunarc/genomes/mouse/GRCm38.p6/gencode.vM19.chr_patch_hapl_scaff.annotation.gtf
head ~/lunarc/genomes/mouse/GRCm38.p6/gencode.vM19.chr_patch_hapl_scaff.annotation.gtf
```

Perfect - now you know how big these gtf files are and which internal structure they have. Sometimes you a want to extract information out of them quickly.

### Finding stuff using `grep`

Get all information regarding "Gapdh" out of the file (using grep) and put this information in a new 'Gapdh.gtf' file in your current folder. You can log this command in a file called 'Gapdh.gtf.log' so you can remind yourself of what you did:.

```
cd ~/NAS/
grep Gapdh ~/lunarc/genomes/mouse/GRCm38.p6/gencode.vM19.chr_patch_hapl_scaff.annotation.gtf > Gapdh.gtf
echo 'grep Gapdh ~/lunarc/genomes/mouse/GRCm38.p6/gencode.vM19.chr_patch_hapl_scaff.annotation.gtf > Gapdh.gtf' > Gapdh.gtf.log
```
`grep` will look over an entire file, but sometimes you want to look just within a specific column. For example, get all genes from chr4 between base positions 10000002 and 19000002 and store the results in a file:

```
fname=~/lunarc/genomes/mouse/GRCm38.p6/gencode.vM19.chr_patch_hapl_scaff.annotation.gtf
awk '{ if ( ($3 == "gene" && $1 == "chr4") && ($4 < 19000002 && $5 > 10000002) ) {print}}' $fname > ~/NAS/TestFileCreation/chr4_10000002_19000002.genes.gtf
```

You can do even more hardcore scripting, and those will go up on Piazza for you to look at.

## Find - a very useful tool!

The Linux/Unix find program is extremely useful if you want to apply any of the before mentioned steps to a list of files/folders. The tool is very powerful and with power comes responsibility. Be sure that your scripts do what they should do - using test files.

Anyhow - 'find' does exactly that - it does find files for you:

```
find
```

It has a lot of options, but commonly used are these: 
- **-name '*.R'** finds all R scripts in the folder and the sub-folders
- **-type d** finds all directories (**f** files)
- **-exec grep test {} +** applies 'grep test' to all identified files
- **-maxdepth 1** stops at the first sub-folder level


For example lets try and find the bam file that you made:

```
find *.bam
```


# You made it!

Practice makes perfect; go through what you have done here today in your own time to consolidate what your have learnt. We will send out a link to a Piazza course for you to get some harder exercises so you can tackle them online together.
